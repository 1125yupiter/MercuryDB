<%*
const date = tp.date.now("YYYY-MM-DD");
const time = tp.date.now("HH:mm");

// 현재 볼트 데이터 자동 수집
const allFiles = app.vault.getMarkdownFiles();
const totalNodes = allFiles.length;

// 오늘 생성된 파일 수
const today = new Date().toDateString();
const newNotesToday = allFiles.filter(file => 
  new Date(file.stat.ctime).toDateString() === today
).length;

// 전체 링크 수 계산
let totalLinks = 0;
let totalBacklinks = 0;

for (const file of allFiles) {
  const cache = app.metadataCache.getFileCache(file);
  if (cache?.links) {
    totalLinks += cache.links.length;
  }
  
  // 백링크 수
  const backlinks = app.metadataCache.getBacklinksForFile(file);
  if (backlinks?.data) {
    totalBacklinks += Object.keys(backlinks.data).length;
  }
}

// 고립된 노드 (링크가 없는 노트)
const isolatedNodes = allFiles.filter(file => {
  const cache = app.metadataCache.getFileCache(file);
  const hasOutlinks = cache?.links?.length > 0;
  const backlinks = app.metadataCache.getBacklinksForFile(file);
  const hasBacklinks = backlinks?.data && Object.keys(backlinks.data).length > 0;
  return !hasOutlinks && !hasBacklinks;
}).length;

// 폴더별 분석
const neuronsFiles = allFiles.filter(f => f.path.startsWith('Neurons/')).length;
const metaFiles = allFiles.filter(f => f.path.startsWith('Meta/')).length;

// 평균 연결도
const avgConnections = totalNodes > 0 ? (totalLinks / totalNodes).toFixed(2) : 0;

// 어제 데이터와 비교 (선택사항)
const yesterday = tp.date.now("YYYY-MM-DD", -1);
const yesterdayLogPath = `Meta/Graph-History/graph-${yesterday}.md`;
let growthData = "신규 분석";

try {
  const yesterdayFile = app.vault.getAbstractFileByPath(yesterdayLogPath);
  if (yesterdayFile) {
    const yesterdayContent = await app.vault.read(yesterdayFile);
    const yesterdayNodes = yesterdayContent.match(/\*\*총 노드\*\*: (\d+)/);
    if (yesterdayNodes) {
      const nodeGrowth = totalNodes - parseInt(yesterdayNodes[1]);
      growthData = `노드 증가: +${nodeGrowth}개`;
    }
  }
} catch (e) {
  // 어제 파일이 없으면 무시
}
-%>

# Graph Analysis <% date %>

## 📊 자동 수집 데이터
- **총 노드 수**: <% totalNodes %>개
- **총 링크 수**: <% totalLinks %>개
- **총 백링크**: <% totalBacklinks %>개
- **고립된 노드**: <% isolatedNodes %>개
- **평균 연결도**: <% avgConnections %>
- **오늘 신규 노트**: <% newNotesToday %>개

## 📁 폴더별 분석
- **Neurons 폴더**: <% neuronsFiles %>개 노트
- **Meta 폴더**: <% metaFiles %>개 노트
- **기타**: <% totalNodes - neuronsFiles - metaFiles %>개 노트

## 📈 성장 분석
- **<% growthData %>**
- **연결 밀도**: <% ((totalLinks * 2) / totalNodes).toFixed(3) %>
- **기록 시간**: <% time %>

## 📸 스크린샷
![[graph-<% date %>.png]]

## 📝 수동 관찰사항
- 

---
**Tags**: #graph-analysis #auto-generated
**Nodes**: <% totalNodes %> | **Links**: <% totalLinks %> | **Density**: <% ((totalLinks * 2) / totalNodes).toFixed(2) %>