## 概要
AION環境で RedisClusterを立ち上げ稼働させるための、概要説明です。  
AIONでは、envoy上に redis-cluster が構築されています。  
envoy上でのredis-cluster構築設定については、下記、もしくは、aion-core-manifest の generated 下にある default.yml を参照してください。   

## 動作環境
・ OS : Linux OS  
・ CPU: ARM/AMD/Intel  
・ Kubernetes  
・ AION  

## Redisについて
Redisは、柔軟性と耐久性を兼ね備えた、インメモリキャッシュ技術とキーバリューデータベースが組み合わされた、プロジェクトリソースです。

AIONは、エッジネットワーククラスター環境での重要なエッジキャッシュDBテクノロジーとして、Redisを基礎インフラ環境として提供します。

エッジ環境はスペックの制限があるため、LatonaおよびAIONでは、機能性とパフォーマンスのバランスに優れているRedisを採用しています。

## ymlファイルの中身（aion-core-manifest内のgenerated下にあるdefault.ymlを参照） 

```      
apiVersion: v1
data:
  envoy.yaml: |
    static_resources:
      listeners:
      - name: redis_listener
        address:
          socket_address:
            address: 0.0.0.0
            port_value: 1999
        filter_chains:
        - filters:
          - name: envoy.filters.network.redis_proxy
            typed_config:
              "@type": type.googleapis.com/envoy.config.filter.network.redis_proxy.v2.RedisProxy
              stat_prefix: egress_redis
              settings:
                op_timeout: 5s
              prefix_routes:
                catch_all_route:
                  cluster: redis_cluster
      clusters:
      - name: redis_cluster
        connect_timeout: 1s
        type: strict_dns # static
        lb_policy: MAGLEV
        load_assignment:
          cluster_name: redis_cluster
          endpoints:
          - lb_endpoints:
            - endpoint:
                address:
                  socket_address:
                    address: redis
                    port_value: 6379

```