<%*
// 날짜 및 시간 설정
const date = tp.date.now("YYYY-MM-DD");
const time = tp.date.now("HH:mm");
const koreanDate = tp.date.now("YYYY년 MM월 DD일");

// 파일명 변경
const fileName = `Graph-Analysis-${date}`;
tp.file.rename(fileName);

// 기본 데이터 수집
const allFiles = app.vault.getMarkdownFiles();
const totalNodes = allFiles.length;

// 오늘 생성된 파일
const today = new Date().toDateString();
const newNotesToday = allFiles.filter(file => 
  new Date(file.stat.ctime).toDateString() === today
).length;

// 링크 수 계산
let totalLinks = 0;
let totalBacklinks = 0;

allFiles.forEach(file => {
  const cache = app.metadataCache.getFileCache(file);
  if (cache && cache.links) {
    totalLinks += cache.links.length;
  }
  
  const backlinks = app.metadataCache.getBacklinksForFile(file);
  if (backlinks && backlinks.data) {
    totalBacklinks += Object.keys(backlinks.data).length;
  }
});

// 고립된 노드 계산
let isolatedNodes = 0;
allFiles.forEach(file => {
  const cache = app.metadataCache.getFileCache(file);
  const hasOutlinks = cache && cache.links && cache.links.length > 0;
  const backlinks = app.metadataCache.getBacklinksForFile(file);
  const hasBacklinks = backlinks && backlinks.data && Object.keys(backlinks.data).length > 0;
  
  if (!hasOutlinks && !hasBacklinks) {
    isolatedNodes++;
  }
});

// 폴더별 분석
const neuronsFiles = allFiles.filter(f => f.path.startsWith('Neurons/')).length;
const metaFiles = allFiles.filter(f => f.path.startsWith('Meta/')).length;
const otherFiles = totalNodes - neuronsFiles - metaFiles;

// 계산된 지표들
const avgConnections = totalNodes > 0 ? (totalLinks / totalNodes).toFixed(2) : "0";
const connectionDensity = totalNodes > 0 ? ((totalLinks * 2) / totalNodes).toFixed(3) : "0";
const networkActivity = totalNodes > 0 ? (((totalNodes - isolatedNodes) / totalNodes) * 100).toFixed(1) : "0";
const neuronsPercent = totalNodes > 0 ? ((neuronsFiles / totalNodes) * 100).toFixed(1) : "0";
const metaPercent = totalNodes > 0 ? ((metaFiles / totalNodes) * 100).toFixed(1) : "0";
const otherPercent = totalNodes > 0 ? ((otherFiles / totalNodes) * 100).toFixed(1) : "0";

// 네트워크 유형 판단
let networkType = "분산형 네트워크";
if (isolatedNodes < totalNodes * 0.2) {
  networkType = "잘 연결된 네트워크";
}

// 연결 패턴 판단
let connectionPattern = "선형적 연결 구조";
if (avgConnections > 2) {
  connectionPattern = "활발한 상호참조";
}

// 성장 단계 판단
let growthStage = "성숙한 네트워크";
if (totalNodes < 100) {
  growthStage = "초기 구축";
} else if (totalNodes < 500) {
  growthStage = "활성 성장";
}
-%>

# 📈 그래프 분석 리포트 - <% koreanDate %>

## 📊 핵심 지표 대시보드

### 🔗 연결성 분석
- **총 노드 수**: <% totalNodes %>개 *전체 노트 파일의 개수*
- **총 링크 수**: <% totalLinks %>개 *노트 간 연결된 링크의 총합*
- **총 백링크**: <% totalBacklinks %>개 *다른 노트에서 참조되는 링크*
- **평균 연결도**: <% avgConnections %> *노드당 평균 링크 수*

### 🏝️ 네트워크 건강도
- **고립된 노드**: <% isolatedNodes %>개 *연결이 없는 독립적인 노트*
- **연결 밀도**: <% connectionDensity %> *전체 네트워크의 연결 정도*
- **네트워크 활성도**: <% networkActivity %>% *연결된 노드의 비율*

### 📈 성장 지표
- **오늘 신규 노트**: <% newNotesToday %>개 *오늘 생성된 새로운 노트*

## 📁 구조별 분석

### 폴더 분포
- **🧠 Neurons 폴더**: <% neuronsFiles %>개 노트 *(<% neuronsPercent %>%)*
- **🔧 Meta 폴더**: <% metaFiles %>개 노트 *(<% metaPercent %>%)*
- **📂 기타 폴더**: <% otherFiles %>개 노트 *(<% otherPercent %>%)*

## 📸 시각적 분석
![[graph-<% date %>.png]]
*그래프 구조의 스냅샷 - <% time %> 기준*

## 🔍 인사이트 & 관찰사항

### 자동 분석 결과
- **네트워크 유형**: <% networkType %>
- **연결 패턴**: <% connectionPattern %>
- **성장 단계**: <% growthStage %>

### 📝 수동 관찰사항
- 

### 💡 개선 제안
- 현재 네트워크 상태를 바탕으로 한 맞춤형 개선안을 작성해보세요

---
**분석 완료 시간**: <% koreanDate %> <% time %>  
**총 노드**: <% totalNodes %> | **총 링크**: <% totalLinks %> | **연결 밀도**: <% connectionDensity %>