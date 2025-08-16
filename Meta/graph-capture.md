<%*
const date = tp.date.now("YYYY-MM-DD");
const time = tp.date.now("HH:mm");
const folderPath = "Meta/Graph-History";

// 폴더 생성
await tp.file.create_new_folder(folderPath);

// 사용자 입력
const notes = await tp.system.prompt("오늘 추가된 노트 수:");
const connections = await tp.system.prompt("새로운 연결/발견사항:");
const observation = await tp.system.prompt("주요 관찰사항:");

// 내용 생성
const content = `# Graph Analysis ${date}

## 📊 메트릭
- **새 노트**: ${notes}개
- **새 연결/발견**: ${connections}
- **캡처 시간**: ${time}

## 📸 스크린샷
![[graph-${date}.png]]
> 스크린샷을 Meta/Graph-History/ 폴더에 저장하세요

## 👀 관찰사항
${observation}

## 📝 메모
- 

---
**Tags**: #graph-analysis #daily`;

// 파일 생성
await tp.file.create_new(content, `${folderPath}/graph-${date}`, true);
-%>