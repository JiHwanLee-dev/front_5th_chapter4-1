## 프론트엔드 배포 워크플로우

<img src="https://github.com/user-attachments/assets/5f897c65-5bc9-4bc6-91d6-d9822b45d54a" alt="프론트엔드 배포 워크플로우" width="800"/>

1. GitHub Repository: 프로젝트의 소스 코드가 저장된 저장소입니다.

2. GitHub Actions: 코드 변경 사항이 발생하면 자동으로 빌드 및 배포 프로세스를 실행하는 CI/CD 도구입니다.

3. Build Process: 예를 들어, npm run build 명령어를 통해 정적 파일을 생성합니다.

4. AWS S3: 빌드된 정적 파일을 저장하고, 정적 웹사이트 호스팅을 제공합니다.

5. AWS CloudFront: S3에 저장된 콘텐츠를 전 세계 엣지 로케이션을 통해 빠르게 제공하는 CDN 서비스입니다.

6. End Users: 최종 사용자는 CloudFront를 통해 웹사이트에 접근하여 콘텐츠를 빠르게 로드할 수 있습니다.

## 주요링크

[S3 링크 ](http://hanghae-chapter-4-1.s3-website.ap-northeast-2.amazonaws.com/)

[cloudFront 링크 ](https://d43x4f1rjhm0e.cloudfront.net/)

## 기본과제

1.  **GitHub Actions과 CI/CD 도구**

    🔄 CI/CD개요

    CI/CD란?

    -   지속적인 통합(CI): 개발자들이 코드 변경 사항을 자주 통합하여 자동으로 빌드 및 테스트를 수행합니다.
    -   지속적인 배포(CD): 테스트를 통과한 코드를 자동으로 배포 환경에 전달하여, 사용자에게 빠르게 제공할 수 있습니다.

    GitHub Actions란?

    GitHub Actions는 GitHub에서 제공하는 CI/CD 플랫폼으로, 리포지토리 내에서 워크플로우를 정의하고 자동화할 수 있습니다.

    🚀 CI/CD 파이프라인 구성

    1. 워크플로우 트리거

        main 브랜치에 코드가 푸시되거나, 수동으로 워크플로우를 실행할 때 트리거됩니다.

    2. 주요 단계

    -   코드 체크아웃: 리포지토리의 코드를 가져옵니다.
    -   의존성 설치: npm ci를 통해 패키지를 설치합니다.
    -   빌드: npm run build를 실행하여 프로젝트를 빌드합니다.
    -   AWS 자격 증명 설정: GitHub Secrets에 저장된 AWS 자격 증명을 사용하여 인증합니다.
    -   S3에 배포: 빌드된 파일을 S3 버킷에 업로드합니다.
    -   CloudFront 캐시 무효화: CloudFront의 캐시를 무효화하여 최신 콘텐츠를 제공합니다.

    ⚙️ GitHub Actions 워크플로우 예시

    .github/workflows/deploy.yml 파일에 다음과 같이 정의합니다:

    ```javascript
        name: Deploy Next.js to S3 and invalidate CloudFront
        on:
        push:
            branches:
            - main
        workflow_dispatch:

        jobs:
        deploy:
            runs-on: ubuntu-latest

            steps:
            - name: Checkout repository
                uses: actions/checkout@v4

            - name: Install dependencies
                run: npm ci

            - name: Build
                run: npm run build

            - name: Configure AWS credentials
                uses: aws-actions/configure-aws-credentials@v1
                with:
                aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
                aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
                aws-region: ${{ secrets.AWS_REGION }}

            - name: Deploy to S3
                run: |
                aws s3 sync out/ s3://${{ secrets.S3_BUCKET_NAME }} --delete

            - name: Invalidate CloudFront cache
                run: |
                aws cloudfront create-invalidation --distribution-id ${{ secrets.CLOUDFRONT_DISTRIBUTION_ID }} --paths "/*"
    ```

    🔐 GitHub Secrets 설정
    워크플로우에서 사용하는 민감한 정보는 GitHub Secrets에 저장합니다

    -   AWS_ACCESS_KEY_ID

    -   AWS_SECRET_ACCESS_KEY

    -   AWS_REGION

    -   S3_BUCKET_NAME

    -   CLOUDFRONT_DISTRIBUTION_ID
        `Medium`

    -   Secrets는 리포지토리의 Settings > Secrets에서 설정할 수 있습니다.

2.  **S3와 스토리지**

    🗄️ AWS S3 및 스토리지 개념

    Amazon S3란?

    Amazon Simple Storage Service(Amazon S3)는 AWS에서 제공하는 객체 스토리지 서비스로, 업계 최고의 확장성, 데이터 가용성, 보안 및 성능을 제공합니다. 모든 규모와 업종의 고객은 Amazon S3를 사용하여 데이터 레이크, 웹 사이트, 모바일 애플리케이션, 백업 및 복원, 아카이브, 엔터프라이즈 애플리케이션, IoT 디바이스, 빅 데이터 분석 등 다양한 사용 사례에서 원하는 양의 데이터를 저장하고 보호할 수 있습니다.

    S3의 주요 특징

    -   확장성: S3는 무제한의 데이터 저장 공간을 제공하며, 필요에 따라 스토리지 용량을 자동으로 조정할 수 있습니다.

    -   내구성 및 가용성: S3는 99.999999999%의 내구성과 99.99%의 가용성을 제공하여 데이터 손실 위험을 최소화합니다.

    -   보안: S3는 다양한 암호화와 액세스 제어 기능을 제공하여 데이터를 안전하게 보호합니다.

    -   비용 효율성: S3는 사용한 만큼만 비용을 지불하는 방식으로, 저장 용량에 따라 비용을 효율적으로 관리할 수 있습니다.

    -   웹 호스팅: S3를 사용하여 정적 웹 페이지나 웹 사이트를 호스팅할 수 있습니다.

    S3의 구성 요소

    -   버킷(Bucket): S3에서 데이터를 저장하는 기본 단위로, 각 버킷은 고유한 이름을 가지며, 데이터를 저장하고 관리하는 컨테이너 역할을 합니다.

    -   객체(Object): 버킷에 저장되는 실제 데이터로, 파일과 해당 파일을 설명하는 메타데이터로 구성됩니다.

    -   키(Key): 객체를 고유하게 식별하는 이름으로, 버킷 내에서 객체를 구분하는 데 사용됩니다.

3.  **CloudFront와 CDN**

    📌 CDN(Content Delivery Network)이란?

    CDN은 콘텐츠 전송 네트워크로, 전 세계에 분산된 서버를 통해 사용자에게 웹 콘텐츠를 빠르고 안정적으로 제공하는 기술입니다. 사용자와 가장 가까운 서버에서 콘텐츠를 전달함으로써 지연 시간을 최소화하고, 웹 사이트의 성능과 안정성을 향상시킵니다.

    ✅ CDN의 주요 기능

    -   지연 시간 감소: 사용자와 가까운 서버에서 콘텐츠를 제공하여 로딩 시간을 단축합니다.

    -   트래픽 분산: 분산된 서버를 통해 트래픽을 분산시켜 서버 과부하를 방지합니다.

    -   보안 강화: DDoS 공격 등으로부터 원본 서버를 보호합니다.

    -   가용성 향상: 서버 장애 시 다른 서버에서 콘텐츠를 제공하여 서비스 중단을 방지합니다.

    🚀 AWS CloudFront란?

    AWS CloudFront는 Amazon Web Services에서 제공하는 CDN 서비스로, 전 세계에 분산된 엣지 로케이션(Edge Location)을 통해 사용자에게 콘텐츠를 빠르게 전달합니다. CloudFront는 S3, EC2, 또는 사용자 지정 서버와 같은 다양한 오리진 서버와 연동하여 콘텐츠를 캐싱하고 배포합니다.

    🔧 CloudFront의 구성 요소

    -   오리진 서버(Origin Server): 원본 콘텐츠를 저장하는 서버로, S3 버킷, EC2 인스턴스 등이 될 수 있습니다.

    -   엣지 로케이션(Edge Location): 전 세계에 분산된 서버로, 사용자와 가까운 위치에서 콘텐츠를 제공하여 지연 시간을 최소화합니다.

    -   디스트리뷰션(Distribution): CloudFront를 통해 콘텐츠를 배포하는 설정 단위로, 웹 또는 RTMP 배포를 구성할 수 있습니다

4.  **캐시 무효화(Cache Invalidation)**

    📌 캐시 무효화 개요

    캐시 무효화(Cache Invalidation)는 CDN(Content Delivery Network)이나 웹 브라우저와 같은 캐시 시스템에서 저장된 콘텐츠를 제거하거나 갱신하여, 사용자에게 최신 정보를 제공하는 과정입니다. 이는 캐시된 콘텐츠가 원본 서버의 변경 사항을 반영하지 못할 때 발생하는 문제를 해결하기 위해 사용됩니다.

    🔍 왜 캐시 무효화가 필요한가?

    -   콘텐츠 업데이트 반영: 웹사이트나 애플리케이션의 콘텐츠가 변경되었을 때, 캐시된 오래된 콘텐츠를 제거하여 최신 정보를 사용자에게 제공합니다.

    -   데이터 일관성 유지: 캐시된 데이터와 원본 데이터 간의 불일치를 방지하여, 사용자에게 정확한 정보를 제공합니다.

    -   보안 강화: 민감한 정보가 포함된 콘텐츠가 변경되었을 때, 이전 캐시를 제거하여 보안을 강화합니다.

    ⚙️ 캐시 무효화 방법

    1. 수동 무효화

        CDN 제공업체의 콘솔이나 API를 통해 특정 파일이나 경로를 지정하여 캐시를 무효화합니다.

        ```bash
        aws cloudfront create-invalidation \
        --distribution-id EXXXXXXXXXXXX \
        --paths "/index.html" "/static/*"
        ```

    2. 자동 무효화

        CI/CD 파이프라인에서 배포 후 자동으로 캐시를 무효화하도록 설정할 수 있습니다.

        ```bash
            - name: Invalidate CloudFront cache
            run: |
                aws cloudfront create-invalidation \
                --distribution-id ${{ secrets.CLOUDFRONT_DISTRIBUTION_ID }} \
                --paths "/*"
        ```

    3. 버전 관리
       파일 이름에 버전 정보를 포함시켜, 새로운 버전의 파일이 배포될 때마다 캐시를 우회하도록 합니다.

        - style.css → style.v1.css

        - app.js → app.v2.js

5.  **Repository secret과 환경변수**

    📌 Repository Secrets란?

    Repository Secrets는 GitHub Actions 워크플로우에서 사용되는 민감한 정보를 안전하게 저장하고 관리할 수 있는 기능입니다. 이러한 정보에는 API 키, 액세스 토큰, 비밀번호 등이 포함됩니다.

    ✅ 주요 특징

    -   보안성: Secrets는 암호화되어 저장되며, 워크플로우 실행 중에만 복호화되어 사용됩니다.

    -   접근 제한: Secrets는 해당 리포지토리의 워크플로우에서만 접근할 수 있습니다.

    -   로깅 보호: 워크플로우 로그에서 Secrets 값은 자동으로 마스킹되어 출력되지 않습니다.

    🛠️ 설정 방법

    1. GitHub 리포지토리의 Settings 탭으로 이동합니다.

    2. 왼쪽 사이드바에서 Secrets and variables > Actions를 선택합니다.

    3. Secrets 탭에서 New repository secret 버튼을 클릭합니다.

    4. Name 필드에 변수명을 입력하고, Secret 필드에 값을 입력한 후 Add secret을 클릭합니다.

    🧪 워크플로우에서의 사용 예시

    ```
        jobs:
        build:
        runs-on: ubuntu-latest
        steps:
        - name: Checkout code
            uses: actions/checkout@v2
        - name: Use secret
            env:
            API_KEY: ${{ secrets.API_KEY }}
            run: echo "Using API key: $API_KEY"
    ```

    🌿 환경 변수(Environment Variables)란?

    환경 변수는 워크플로우에서 반복적으로 사용되는 값을 정의하여 코드의 재사용성과 가독성을 높이는 데 사용됩니다. 비밀 정보가 아닌 일반적인 설정 값에 적합합니다.

    ✅ 주요 특징

    -   유연성: 워크플로우, 잡, 또는 스텝 수준에서 정의할 수 있습니다.

    -   가시성: 환경 변수의 값은 워크플로우 로그에 출력될 수 있으므로, 비밀 정보는 저장하지 않아야 합니다.

    -   재사용성: 여러 스텝에서 동일한 값을 사용할 수 있어 코드 중복을 줄일 수 있습니다.

    🛠️ 설정 방법

    1.  GitHub 리포지토리의 Settings 탭으로 이동합니다.

    2.  왼쪽 사이드바에서 Secrets and variables > Actions를 선택합니다.

    3.  Variables 탭에서 New repository variable 버튼을 클릭합니다.

    4.  Name 필드에 변수명을 입력하고, Value 필드에 값을 입력한 후 Add variable을 클릭합니다.

    🧪 워크플로우에서의 사용 예시

    ```
        jobs:
        build:
        runs-on: ubuntu-latest
        env:
        NODE_ENV: production
        API_URL: ${{ vars.API_URL }}
        steps:
        - name: Checkout code
            uses: actions/checkout@v2
        - name: Install dependencies
            run: npm install
    ```

    🛡️ 보안 권장 사항

    -   민감한 정보는 반드시 Repository Secrets에 저장하고, 환경 변수에는 저장하지 않도록 합니다.

    -   Secrets와 환경 변수의 이름은 명확하게 구분하여 관리합니다.

    -   워크플로우 로그에 비밀 정보가 출력되지 않도록 주의합니다.

    -   필요하지 않은 Secrets와 환경 변수는 주기적으로 정리하여 보안을 강화합니다.

## 성능 비교 분석 (S3 vs CloudFront)

### 📊 Lighthouse 성능 측정 결과

#### S3 호스팅 ([http://hanghae-chapter-4-1.s3-website.ap-northeast-2.amazonaws.com/](http://hanghae-chapter-4-1.s3-website.ap-northeast-2.amazonaws.com/))

| 측정 항목                      | 점수        |
| ------------------------------ | ----------- |
| Performance                    | [72]        |
| First Contentful Paint (FCP)   | [0.3s]      |
| Largest Contentful Paint (LCP) | [0.8s]      |
| Time to Interactive (TTI)      | [시간 입력] |
| Total Blocking Time (TBT)      | [860ms]     |
| Cumulative Layout Shift (CLS)  | [0]         |

#### CloudFront 배포 ([https://d43x4f1rjhm0e.cloudfront.net/](https://d43x4f1rjhm0e.cloudfront.net/))

| 측정 항목                      | 점수        |
| ------------------------------ | ----------- |
| Performance                    | [71]        |
| First Contentful Paint (FCP)   | [0.3s]      |
| Largest Contentful Paint (LCP) | [0.3s]      |
| Time to Interactive (TTI)      | [시간 입력] |
| Total Blocking Time (TBT)      | [1050ms]    |
| Cumulative Layout Shift (CLS)  | [0]         |

### 🔍 성능 차이 분석

1. **응답 시간 (Latency)**

    - S3: FCP 0.3s, LCP 0.8s로 초기 로딩 시간이 다소 긴 편입니다. TBT는 860ms로 측정되어 상호작용 지연이 발생합니다.
    - CloudFront: FCP 0.3s, LCP 0.3s로 콘텐츠가 더 빠르게 표시되며, 특히 LCP가 S3 대비 0.5초 더 빠릅니다.
    - 차이점: CloudFront가 LCP에서 눈에 띄는 성능 향상을 보여주나, TBT는 1050ms로 오히려 더 높게 나타났습니다. 이는 JavaScript 실행 시간과 관련이 있을 수 있습니다.

2. **캐싱 효과**

    - S3: 캐싱 기능이 없어 매 요청마다 원본 서버에서 콘텐츠를 가져와야 합니다. 이로 인해 LCP가 0.8s로 상대적으로 높게 측정됩니다.
    - CloudFront: 엣지 로케이션에서의 캐싱으로 LCP가 0.3s로 크게 개선되었습니다. 특히 정적 자산들의 전송 속도가 향상되었습니다.
    - 영향: 캐싱을 통해 LCP가 62.5% 개선되었으며, 이는 사용자 경험에 직접적인 영향을 미치는 중요한 성능 향상입니다.

3. **지리적 위치에 따른 영향**
    - S3: 서울 리전(ap-northeast-2)에서만 서비스되어, 한국 사용자에게는 빠른 응답을 제공하지만 해외 사용자들에게는 지연이 발생할 수 있습니다.
    - CloudFront: 전 세계 엣지 로케이션을 통한 서비스로, 사용자의 위치와 관계없이 일관된 성능을 제공합니다.
    - 결과: 현재 측정은 한국에서 진행되어 큰 차이가 없으나, 해외 사용자의 경우 CloudFront를 통한 배포가 훨씬 빠른 응답 시간을 제공할 것으로 예상됩니다.

### 💡 결론

CloudFront 배포가 전반적으로 더 나은 성능을 보여주고 있습니다. 특히 다음과 같은 이점이 있습니다:

1. LCP가 0.5초 더 빠르며, 이는 사용자가 체감하는 로딩 속도에 직접적인 영향을 줍니다.
2. 캐싱을 통한 정적 자산 제공으로 서버 부하가 감소합니다.
3. 글로벌 사용자에게 일관된 성능을 제공할 수 있습니다.

다만, TBT가 더 높게 측정된 부분은 추가적인 최적화가 필요한 영역으로 보입니다.

### 📝 측정 환경

-   측정 도구: Lighthouse (Chrome DevTools)
-   네트워크 환경: 유선 인터넷 (한국 서울)
-   측정 일시: 2025년 5월
-   측정 횟수: 1회 측정의 평균값
