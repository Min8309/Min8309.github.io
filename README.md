# 오픈갤러리 UI 구현 포트폴리오 — 핵심 기술과 코드 구현

그림 렌탈·아트테크·아트굿즈 탐색을 제공하는 React 프런트엔드의 기술 분석 문서입니다.

## 발표 자료 다운로드

- [PPTX 발표 자료](docs/open-gallery-technology.pptx)
- [PDF 발표 자료](docs/open-gallery-technology.pdf)

참고 자료 `senior-helper (1).pdf`의 기획 → 사용자 경험 → 기술 구조 → 코드 해설 → 검증 → 개선 순서를 반영했습니다. 참고 프로젝트의 기술·성과를 이 사이트의 구현으로 옮기지 않았습니다.

## 포트폴리오에서의 설명

이 프로젝트는 React로 작품·상품 탐색 UI와 상태 기반 인터랙션을 구현한 프런트엔드 포트폴리오로 소개하기에 적합합니다. 공식 서비스 개발·운영이나 실제 결제 구축 경험으로 표현하지 않습니다. 본인이 직접 담당한 범위와 구현 기간은 실제 이력에 맞게 별도로 기입하세요. 브랜드·이미지의 출처와 사용 권한을 확인하고, 원 서비스와의 관계를 명확히 표시하세요.

## 분석 기준과 범위

- 저장소: https://github.com/Min8309/Min8309.github.io
- 기준 커밋: `5b25031936547bbd71c9a26cf28d9839fbf4e926`
- 사이트 주소: https://min8309.github.io/ (이번 작업에서 접속·화면 검증 미완료)
- 근거: `index.html`, `asset-manifest.json`, `static/js/main.e1ecce0f.js`, `static/css/main.05e50143.css`, `img/`
- 현재 main에는 빌드 산출물이 있으며 `src/`, `package.json`, 잠금 파일을 확인하지 못했습니다. 정확한 React 버전이나 개발 도구 설정은 확정하지 않습니다.
- 발표 자료의 코드는 난독화된 빌드 코드의 **동작을 재구성한 예제**입니다. 원본 컴포넌트 이름·파일 경로·줄 번호로 제시하지 않습니다.

## 핵심 기술

| 기술 | 구현 역할 |
| --- | --- |
| React / React DOM | 컴포넌트 렌더링, createRoot, StrictMode |
| useState | 이미지·수량·기종·배너·찜 등의 화면 상태 |
| useEffect | 타이머 관리, 상품 변경 시 상태 초기화 |
| React Router | 페이지 이동, 상세 ID 조회, Link 내비게이션 |
| JavaScript 배열 처리 | map 목록 렌더링, find 상세 조회, filter 작품 선별 |
| CSS Grid / Flex / sticky | 목록·상세 레이아웃과 상세 탭 고정 |
| 정적 JS·CSS·이미지 | 브라우저 실행용 배포 파일; CRA 기본 메타데이터 흔적 |

## 페이지 구성

| 경로 | 내용 |
| --- | --- |
| `/` | 메인 배너, 작품·후기·서비스 소개 |
| `/art-tech` | 아트테크 작품 목록 |
| `/artwork/:id` | 작품 상세와 공간 미리보기 |
| `/goods` | 아트굿즈 목록·배너 |
| `/goods/:id` | 상품 상세·옵션·수량·금액 |
| `/login` | 로그인 입력 UI |

## 핵심 구현 예제

아래 예제의 변수명과 JSX는 설명을 위해 재구성했습니다. 실제 근거는 [분석 기준 JS 번들](https://github.com/Min8309/Min8309.github.io/blob/5b25031936547bbd71c9a26cf28d9839fbf4e926/static/js/main.e1ecce0f.js)입니다.

### 1. URL과 상세 상품 연결

```jsx
const { id } = useParams();
const product = products.find(item => item.id === Number(id));
if (!product) return <div>상품을 찾을 수 없습니다.</div>;
```

URL의 문자열 ID를 숫자로 변환해 데이터 배열에서 조회합니다. 없는 상품은 안내 화면으로 처리합니다.

### 2. 배너 자동 전환과 타이머 정리

```jsx
useEffect(() => {
  if (paused) return;
  const timer = setInterval(() => {
    setIndex(i => i === slides.length - 1 ? 0 : i + 1);
  }, 5000);
  return () => clearInterval(timer);
}, [paused]);
```

아트굿즈 배너는 5초 순환이며 마우스 진입 시 정지합니다. 메인 배너는 별도 4초 타이머입니다.

### 3. 연관 옵션 초기화

```jsx
function changeDevice(value) {
  setDevice(value);
  setModel("");
}
// 기기 종류 미선택 시 세부 기종 select 비활성화
```

상위 기기가 바뀌면 이전 기종이 남지 않게 초기화합니다.

### 4. 수량과 총액

```jsx
const total = device && model ? product.price * quantity : 0;
const decrease = () => setQuantity(q => q > 1 ? q - 1 : 1);
const increase = () => setQuantity(q => q + 1);
```

수량은 최소 1개이며 옵션 선택 후 총액을 계산합니다. 가격 표시는 `toLocaleString()`을 사용합니다. 서버 결제 검증 구현은 아닙니다.

### 5. 썸네일과 상세 상태

썸네일의 `onMouseEnter`, `onFocus`, `onClick`이 대표 이미지를 갱신합니다. 상품 변경 시 `useEffect`에서 이미지·수량·기기·기종·찜을 초기화합니다. 찜은 메모리 상태로 영구 저장이 아닙니다.

## 구현과 시연의 구분

- 구현 확인: 라우팅, 배너 순환, 상품 조회, 이미지 선택, 기종 옵션, 수량·금액, 찜 토글, 작품 공간·배경 선택 상태.
- 시연 처리: 로그인 submit은 알림 후 메인 이동, 구매·장바구니·선물하기는 옵션 확인과 알림 중심.
- 추가 연결 필요: 실제 인증·소셜 로그인·결제·주문 저장·회원가입 완료 흐름.
- 화면의 작품 수·거래 수 등 소개 문구를 검증된 서비스 성과나 실시간 데이터로 해석하지 않습니다.

## 확인한 개선 과제

1. 상품 배열의 `id: 3` 중복으로 서로 다른 카드가 같은 상세로 연결될 수 있습니다.
2. `/signup` 링크에 대응하는 라우트가 없습니다.
3. `React App`, 기본 description, `lang="en"` 등 메타데이터 보완이 필요합니다.
4. 고정 폭 레이아웃의 모바일 넘침과 키보드·배너·메뉴 접근성은 실제 기기에서 점검해야 합니다.
5. 원본 소스와 의존성 파일을 확보해 실행·빌드 재현성을 마련해야 합니다.

## 검증 상태

- 수행: 저장소 파일·번들 정적 분석, 별도 실행용 번들의 `node --check` 문법 검사.
- 수행: 발표 파일 생성 후 PPTX 구조·PDF 페이지 수·텍스트 내용 검사.
- 미수행: 원본 프로젝트 빌드·단위 테스트(개발 원본 부재), 실제 브라우저 동작, 모바일, 인증·결제, 배포 상태 검증.
- 참고 자료의 테스트 통과 수치나 서버 기술을 이 프로젝트 결과로 사용하지 않았습니다.

## 발표 자료 구성

20쪽: 프로젝트 소개 / 기획 / 대상과 목표 / 사용자 흐름 / 구조 / 기술 / 경로 / 핵심 코드 6개 / 작품 미리보기 / 구현 범위 / 개선 / 검증 / 다음 목표 / 시연 / 근거.

## 실행 안내

현재 저장소는 빌드 산출물입니다. 원본 개발용 `npm install`·`npm run build` 명령은 확인할 수 없습니다. 정적 서버로 열 경우 `/goods/1` 같은 경로의 직접 접근에 `index.html` 폴백이 필요합니다. 발표 전 실제 호스팅 환경에서 새로고침과 직접 진입을 확인하세요.
