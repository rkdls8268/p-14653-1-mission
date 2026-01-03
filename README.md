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
