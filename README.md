## 概要
AION環境で RedisClusterを立ち上げ稼働させるための、概要説明です。    
redis-cluster構築設定については、下記、もしくは、aion-core-manifests/template/bases/redis/deployment.yml を参照してください。   

## 動作環境
・ OS : Linux OS  
・ CPU: ARM/AMD/Intel  
・ Kubernetes  
・ AION  

## Redisについて
Redisは、柔軟性と耐久性を兼ね備えた、インメモリキャッシュ技術とキーバリューデータベースが組み合わされた、プロジェクトリソースです。

AIONは、エッジネットワーククラスター環境での重要なエッジキャッシュDBテクノロジーとして、Redisを基礎インフラ環境として提供します。

エッジ環境はスペックの制限があるため、LatonaおよびAIONでは、機能性とパフォーマンスのバランスに優れているRedisを採用しています。  


## AION における Redis のデプロイ・稼働
[aion-core-manifests](https://github.com/latonaio/aion-core-manifests)の template/bases/redis/deployment.yml に redis-cluster をデプロイ・稼働させるために必要なyamlファイルが配置されています。

ymlファイル（deployment.yml）の中身
```      
apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
kind: Deployment
metadata:
  name: redis-cluster
spec:
  selector:
    matchLabels:
      app: redis-cluster
  strategy:
    type: Recreate
  template:
    metadata:
      labels:
        app: redis-cluster
    spec:
      containers:
        - image: redis:6.0-rc-alpine3.11
          name: redis-cluster
          args:
            - --timeout
            - "600"
            - --loglevel
            - "debug"
          resources:
            limits:
              memory: 512Mi
              cpu: 100m
            requests:
              memory: 50Mi
              cpu: 5m
          ports:
            - containerPort: 6379
              name: redis-cluster

```