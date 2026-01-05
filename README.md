# p-14653-1-mission
0001, 0002, 0003, 0004, 0005 완료

### 6. Deployment
Deployment 생성하기

### 7. Scaling
스케일링: 트래픽이 늘어나면 서버를 더 투입하고, 줄어들면 서버를 줄이는 것
|종류|설명|예시|
|---|---|---|
|수평 스케일링(Horizontal)|인스턴스 수를 늘림|Pod 3개 -> 5개|
|수직 스케일링(Vertical)|인스턴스 성능을 높임|CPU 1코어 -> 4코어|

쿠버네티스는 수평 스케일링에 특화

⭐️ 자동복구: 강력한 기능 중 하나
* Pod가 죽으면 자동으로 새 Pod 생성
* ReplicaSet이 "원하는 상태"를 유지하려 함

### 8. 롤링 업데이트
새로운 버전의 앱을 배포할 때 한 번에 모든 pod를 교체하면 서비스가 중단되는 것을 막기 위해 pod 하나씩 무중단으로 배포하는 업데이트 방식

장점
* 무중단 배포: 항상 일부 pod가 서비스 중
* 안전한 배포: 문제 발생 시 롤백 가능
* 점진적 검증: 새 버전이 안정적인지 확인하면서 배포

```bash
# 롤아웃 상태 확인 (실시간)
kubectl rollout status deployment/nginx-deployment
```

```bash
# 업데이트 이력 확인
kubectl rollout history deployment/nginx-deployment
```

```bash
# 이전 버전으로 롤백
kubectl rollout undo deployment/nginx-deployment
```

```bash
# 롤백 확인
kubectl describe deployment nginx-deployment
```

### 9. ClusterIP Service
service는 왜 필요한가?
pod는 일시적임. 삭제되고 다시 만들어지면 IP 주소가 바뀐다.

역할
* 고정된 접점 제공: pod가 바뀌어도 service 이름/IP는 유지
* 로드 밸런싱: 여러 pod에 트래픽 분산
* 서비스 디스커버리: 이름으로 pod를 찾을 수 있음

|종류|접근 범위|사용 사례|
|---|---|---|
|ClusterIP|클러스터 내부만|백엔드 서비스, DB|
|NodePort|외부에서 노드IP:포트로|개발/테스트 환경|
|Load Balancer|외부 로드밸런서|클라우드 프로덕션|

### 10. NodePort Service
NodePort: ClusterIP는 클러스터 내부에서만 접근 가능. 외부에서 접근하려면 NodePort 사용
특징
* 모든 노드의 특정 포트를 개방
* 포트 범위: 30000~32767
* 어떤 노드로 접속해도 같은 서비스에 연결됨

### 11. LoadBalancer Service
LoadBalancer: 클라우드 환경에서 외부 로드 밸런서를 자동으로 생성하는 Service

||NodePort|LoadBalancer|
|---|---|---|
|외부 IP|노드 IP 사용|전용 공인 IP 할당|
|포트|30000-32767|원하는 포트 (80,443 등)|
|로드밸런싱|클라이언트가 노드 선택|로드밸런서가 분산|
|사용 환경|개발/테스트|프로덕션|

### 12. ConfigMap
왜 필요한가?
애플리케이션 설정(DB 주소, 포트, 환경 변수 등)을 코드에 하드코딩할 수 없음!
* 설정과 코드 분리: 이미지 재빌드 불필요
* 환경별 설정: dev, staging, prod 다르게 적용
* 중앙 관리: 한 곳에서 설정 관리

### 13. Secret
Secret vs ConfigMap

||ConfigMap|Secret|
|---|---|---|
|용도|일반 설정|민감한 정보|
|예시|로그 레벨, 포트 번호|비밀번호, API 키, 인증서|
|저장 방식|평문|Base64 인코딩|
|메모리 저장|	디스크|	tmpfs (메모리, 더 안전)|

### 14. PersistentVolume과 PersistentVolumeClaim
Pod는 일시적이어서 Pod가 삭제되면 그 안의 데이터도 사라지므로 PersistentVolume이라는 스토리지를 사용
사용 사례
* 데이터베이스
* 파일 업로드 저장소
* 로그 영구 보관
* 캐시 데이터

PersistentVolumn(PV)
* 클러스터 레벨의 스토리지 리소스
* 관리자가 미리 프로비저닝
* 실제 스토리지(디스크, NFS 등)를 최소화

PersistenceVolumeClaim(PVC)
* Pod가 PV를 요청하는 방법
* 개발자가 필요한 용량과 접근 모드 명시
* 조건에 맞는 PV와 자동 연결
⚠️ 삭제 순서 주의: Pod → PVC → PV 순서로 삭제! PVC가 Pod에서 사용 중이면 삭제가 pending 상태로 대기.

### 15. 전체 애플리케이션 배포

구조

<img width="400" height="1200" alt="image" src="https://github.com/user-attachments/assets/24449547-4892-499c-be69-5e5ff239310d" />

