<%*
// 날짜 및 시간 설정
const date = tp.date.now("YYYY-MM-DD");
const time = tp.date.now("HH:mm");
const koreanDate = tp.date.now("YYYY년 MM월 DD일");

// 파일을 Meta/graphview-history/로 이동 및 이름 변경
const targetFolder = "Meta/graphview-history";
const fileName = `Graph-Analysis-${date}`;
const targetPath = `${targetFolder}/${fileName}`;

// 폴더가 없으면 생성
if (!await tp.file.exists(targetFolder)) {
    await app.vault.createFolder(targetFolder);
}

// 파일 이동 및 이름 변경
await tp.file.move(targetPath);

// Neurons 폴더의 파일만 수집
const allFiles = app.vault.getMarkdownFiles();
const neuronsFiles = allFiles.filter(f => f.path.startsWith('Neurons/'));
const totalNodes = neuronsFiles.length;

// 오늘 생성된 노트
const today = new Date().toDateString();
const newNotesToday = neuronsFiles.filter(file => 
  new Date(file.stat.ctime).toDateString() === today
).length;

// 이번 주 생성된 노트
const oneWeekAgo = new Date();
oneWeekAgo.setDate(oneWeekAgo.getDate() - 7);
const newThisWeek = neuronsFiles.filter(file => 
  new Date(file.stat.ctime) > oneWeekAgo
).length;

// 링크 분석 (Neurons 폴더 내부만)
let totalLinks = 0;
let isolatedNodes = 0;
let nodeConnections = new Map();

neuronsFiles.forEach(file => {
  const cache = app.metadataCache.getFileCache(file);
  const outlinks = cache?.links?.length || 0;
  
  // Neurons 폴더 내부 링크만 카운트
  let internalOutlinks = 0;
  if (cache && cache.links) {
    cache.links.forEach(link => {
      const linkedFile = app.metadataCache.getFirstLinkpathDest(link.link, file.path);
      if (linkedFile && linkedFile.path.startsWith('Neurons/')) {
        internalOutlinks++;
      }
    });
  }
  
  // 백링크 계산 (Neurons 내부만)
  const backlinks = app.metadataCache.getBacklinksForFile(file);
  let internalBacklinks = 0;
  if (backlinks && backlinks.data) {
    Object.keys(backlinks.data).forEach(linkPath => {
      if (linkPath.startsWith('Neurons/')) {
        internalBacklinks++;
      }
    });
  }
  
  const totalConn = internalOutlinks + internalBacklinks;
  totalLinks += internalOutlinks;
  
  // 고립된 노드 체크
  if (totalConn === 0) {
    isolatedNodes++;
  }
  
  // 허브 노드 데이터 수집
  nodeConnections.set(file.basename, totalConn);
});

// Top 5 허브 노드
const hubNodes = Array.from(nodeConnections.entries())
    .sort((a, b) => b[1] - a[1])
    .slice(0, 5);

// 태그 분석 (Neurons 폴더만)
let tagsMap = new Map();
neuronsFiles.forEach(file => {
    const cache = app.metadataCache.getFileCache(file);
    if (cache && cache.tags) {
        cache.tags.forEach(tag => {
            tagsMap.set(tag.tag, (tagsMap.get(tag.tag) || 0) + 1);
        });
    }
});

const topTags = Array.from(tagsMap.entries())
    .sort((a, b) => b[1] - a[1])
    .slice(0, 5);
const totalTags = tagsMap.size;

// 핵심 지표 계산
const avgConnections = totalNodes > 0 ? (totalLinks / totalNodes).toFixed(1) : "0";
const isolationRate = totalNodes > 0 ? ((isolatedNodes / totalNodes) * 100).toFixed(1) : "0";
const connectionRate = totalNodes > 0 ? (((totalNodes - isolatedNodes) / totalNodes) * 100).toFixed(1) : "0";

// 성장률 계산 (30일 기준)
const thirtyDaysAgo = new Date();
thirtyDaysAgo.setDate(thirtyDaysAgo.getDate() - 30);
const oldNodes = neuronsFiles.filter(file => 
    new Date(file.stat.ctime) < thirtyDaysAgo
).length;
const monthlyGrowth = oldNodes > 0 ? 
    (((totalNodes - oldNodes) / oldNodes) * 100).toFixed(1) : "새로운 Vault";

// 네트워크 상태 판단
let networkStatus = "🟢 건강";
let statusColor = "green";
if (isolationRate > 50) {
  networkStatus = "🔴 연결 부족";
  statusColor = "red";
} else if (isolationRate > 30) {
  networkStatus = "🟡 개선 필요";
  statusColor = "yellow";
}

// 성장 추세
let growthTrend = "📈 성장중";
if (newThisWeek === 0) {
  growthTrend = "📉 정체";
} else if (newThisWeek > 10) {
  growthTrend = "🚀 급성장";
}

// 허브 노드 텍스트
let hubText = "";
if (hubNodes.length > 0) {
  hubNodes.forEach((node, index) => {
    if (node[1] > 0) {
      hubText += `${index + 1}. **[[${node[0]}]]** - ${node[1]}개 연결\n`;
    }
  });
} else {
  hubText = "*아직 연결된 노트가 없습니다*\n";
}

// 태그 텍스트
let tagText = "";
if (topTags.length > 0) {
  tagText = topTags.map(tag => `\`${tag[0]}\` (${tag[1]})`).join(" • ");
} else {
  tagText = "*태그가 사용되지 않았습니다*";
}

// 개선 제안
let suggestions = [];
if (isolationRate > 30) {
  suggestions.push(`💡 ${isolatedNodes}개의 고립된 노트를 다른 노트와 연결해보세요`);
}
if (avgConnections < 2) {
  suggestions.push("💡 노트 간 상호 참조를 늘려 지식 네트워크를 강화하세요");
}
if (totalTags < totalNodes * 0.5) {
  suggestions.push("💡 태그를 활용해 노트를 체계적으로 분류해보세요");
}
if (newThisWeek === 0) {
  suggestions.push("💡 새로운 인사이트를 기록해 네트워크를 확장해보세요");
}
if (suggestions.length === 0) {
  suggestions.push("✨ 네트워크가 건강하게 성장하고 있습니다!");
}
-%>
# 📊 Neurons 그래프 분석 - <% koreanDate %>

## 🎯 현재 상태: <% networkStatus %>

### 📈 핵심 지표
| 지표 | 값 | 변화 |
|------|-----|------|
| **총 노트** | <% totalNodes %>개 | <% growthTrend %> |
| **총 링크** | <% totalLinks %>개 | 평균 <% avgConnections %>개/노트 |
| **연결률** | <% connectionRate %>% | <% isolatedNodes %>개 고립 |
| **이번 주 신규** | <% newThisWeek %>개 | 오늘 +<% newNotesToday %> |

### 🏆 가장 연결이 많은 노트
<% hubText %>

### 🏷️ 주요 태그
<% tagText %>

## 💡 개선 제안
<% suggestions.join("\n") %>

## 📸 네트워크 시각화
![[graph-<% date %>.png]]
*<% time %> 기준 스냅샷*

---
> 📅 **<% koreanDate %>** | 🧠 **Neurons**: <% totalNodes %>개 | 🔗 **Links**: <% totalLinks %>개 | 📈 **월간 성장**: <% monthlyGrowth %>%