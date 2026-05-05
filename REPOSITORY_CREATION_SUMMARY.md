# Codex 저장소 생성 요약

## 개요

이 문서는 로그인된 GitHub 계정에 `codex` 저장소를 생성하기 위해 진행한 내용을 정리합니다.

## 저장소 정보

- 저장소 이름: `codex`
- 소유자: `Arsenalish`
- URL: https://github.com/Arsenalish/codex
- 공개 여부: Public
- 초기화 방식: GitHub API를 통해 초기 README와 함께 생성

## 진행 과정

1. Codex 환경에서 GitHub CLI(`gh`)를 사용할 수 있는지 확인했습니다.
2. `gh`가 설치되어 있지 않은 것을 확인했고, 대신 GitHub REST API를 사용하기로 했습니다.
3. Codex 프로세스에서 GitHub 토큰을 읽을 수 있는지 확인했습니다.
4. 처음에는 토큰이 별도 PowerShell 세션에만 설정되어 있어 Codex에서 확인되지 않았습니다.
5. Windows 사용자 환경변수 `GITHUB_TOKEN`으로 토큰을 저장했습니다.
6. Codex 앱을 재시작해 환경변수가 앱에 반영되도록 했습니다.
7. GitHub API를 통해 저장소 생성을 시도했습니다.
8. 첫 번째 토큰에는 저장소 생성 권한이 없어 `403 Forbidden` 응답을 받았습니다.
9. 저장소 생성 권한이 있는 토큰으로 권한을 수정했습니다.
10. Codex 프로세스에 남아 있던 이전 토큰 대신 사용자 환경변수의 최신 `GITHUB_TOKEN`을 우선해서 읽도록 했습니다.
11. 최종적으로 https://github.com/Arsenalish/codex 에 저장소를 성공적으로 생성했습니다.

## 참고 사항

- 토큰 값은 출력하거나 이 문서에 저장하지 않았습니다.
- 성공한 요청은 인증된 사용자의 저장소를 생성하는 GitHub REST API 엔드포인트를 사용했습니다.
- 마지막 문제는 Codex 프로세스 환경에 이전 토큰이 남아 있었기 때문에 발생했으며, 사용자 환경변수에 저장된 최신 값을 우선해서 읽는 방식으로 해결했습니다.

## Troubleshooting

### 한글 인코딩 깨짐

GitHub에 한국어 Markdown 파일을 업로드한 뒤 한글이 `Codex ?�?μ냼 ?앹꽦 ?붿빟`처럼 깨져 보이는 문제가 있었습니다.

원인은 PowerShell의 `Get-Content`가 UTF-8 한글 파일을 잘못 해석한 뒤, 깨진 문자열을 다시 Base64로 변환해 GitHub API에 업로드했기 때문입니다.

해결 방법은 파일을 문자열로 읽지 않고, 원본 바이트를 그대로 읽어 Base64로 변환하는 것입니다.

```powershell
$content = [Convert]::ToBase64String(
    [System.IO.File]::ReadAllBytes((Resolve-Path -LiteralPath 'REPOSITORY_CREATION_SUMMARY.md'))
)
```

이 방식으로 다시 덮어쓰기 커밋한 뒤 GitHub에서 한글이 정상적으로 표시되었습니다.
