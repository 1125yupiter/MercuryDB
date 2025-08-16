# 🧠 Neurons 태그 네트워크 현황 - 2025년 08월 16일

## 📊 현황 지표
- 총 노트: 7개
- 고유 태그: 35개
- 총 태그 사용: 35회
- 평균 태그/노트: 5.0
- 태그 있는 노트 비율: 100%
- 다중 태그 노트 비율: 100%
- 태그 없는 노트: 0개

## 🏆 Top 10 태그
1. `#service/site-to-site-vpn` (1)
2. `#architecture/high-availability` (1)
3. `#network/hybrid-connectivity` (1)
4. `#concept/fault-tolerance` (1)
5. `#infrastructure/redundancy` (1)
6. `#service/sagemaker` (1)
7. `#technique/regularization` (1)
8. `#problem/overfitting` (1)
9. `#parameter/hyperparameter-tuning` (1)
10. `#ml/model-optimization` (1)

## 📸 네트워크 시각화
![[graph-2025-08-16.png]]
*10:35 기준*

<%*
/**
 * Neurons 태그 네트워크 "현재 현황"만 기록하는 간소/안전 버전 (Obsidian Templater)
 * - 이동 대상 폴더 자동 생성
 * - 파일명에 .md 확장자 보장
 * - 옵셔널 체이닝 제거 (호환성↑)
 * - frontmatter/in-body 태그 모두 수집, 비문자 태그 안전 처리
 */

const date = tp.date.now("YYYY-MM-DD");
const time = tp.date.now("HH:mm");
const koreanDate = tp.date.now("YYYY년 MM월 DD일");

// ===== 파일 이동(안전) =====
const targetDir = "Meta/graphview-history";
const targetName = `Graph-Analysis-${date}.md`;
async function ensureFolder(path) {
  try { await app.vault.createFolder(path); } catch (e) { /* 이미 있으면 무시 */ }
}
await ensureFolder(targetDir);
try {
  await tp.file.move(`${targetDir}/${targetName}`);
} catch (e) {
  // 이동 실패해도 기록은 진행
}

// ===== 데이터 수집 =====
const allFiles = app.vault.getMarkdownFiles();
const neuronsFiles = allFiles.filter(f => typeof f.path === "string" && f.path.startsWith("Neurons/"));
const totalNotes = neuronsFiles.length;

// ===== 태그 집계 =====
const tagMap = new Map();
let untaggedNotes = 0;
let multiTagNotes = 0;
let totalTagUsage = 0;

function addTagToNote(noteTags, raw) {
  if (raw === null || raw === undefined) return;
  const s = String(raw).trim();
  if (!s) return;
  const tag = s.startsWith("#") ? s : `#${s}`;
  if (!noteTags.has(tag)) {
    noteTags.add(tag);
    tagMap.set(tag, (tagMap.get(tag) || 0) + 1);
    totalTagUsage++;
  }
}

for (const file of neuronsFiles) {
  const cache = app.metadataCache.getFileCache(file) || {};
  const noteTags = new Set();

  // in-body tags
  if (Array.isArray(cache.tags)) {
    for (const t of cache.tags) {
      if (t && typeof t.tag !== "undefined") addTagToNote(noteTags, t.tag);
    }
  }

  // frontmatter tags
  if (cache.frontmatter && typeof cache.frontmatter === "object" && typeof cache.frontmatter.tags !== "undefined") {
    const fm = cache.frontmatter.tags;
    const list = Array.isArray(fm) ? fm : [fm];
    for (const t of list) addTagToNote(noteTags, t);
  }

  if (noteTags.size === 0) untaggedNotes++;
  if (noteTags.size > 1) multiTagNotes++;
}

// ===== 지표 계산 =====
const uniqueTags = tagMap.size;
const avgTagsPerNote = totalNotes ? (totalTagUsage / totalNotes).toFixed(1) : "0";
const taggedRate = totalNotes ? (((totalNotes - untaggedNotes) / totalNotes) * 100).toFixed(0) : "0";
const connectionRate = totalNotes ? ((multiTagNotes / totalNotes) * 100).toFixed(0) : "0";

// ===== Top 태그 =====
const topTags = Array.from(tagMap.entries()).sort((a, b) => b[1] - a[1]).slice(0, 10);
const topTagsLines = topTags.length
  ? topTags.map(([t, c], i) => `${i + 1}. \`${t}\` (${c})`).join('\n')
  : '*아직 없음*';

// ===== 출력 =====
tR = `# 🧠 Neurons 태그 네트워크 현황 - ${koreanDate}

## 📊 현황 지표
- 총 노트: ${totalNotes}개
- 고유 태그: ${uniqueTags}개
- 총 태그 사용: ${totalTagUsage}회
- 평균 태그/노트: ${avgTagsPerNote}
- 태그 있는 노트 비율: ${taggedRate}%
- 다중 태그 노트 비율: ${connectionRate}%
- 태그 없는 노트: ${untaggedNotes}개

## 🏆 Top 10 태그
${topTagsLines}

## 📸 네트워크 시각화
![[graph-${date}.png]]
*${time} 기준*
`;
%>
