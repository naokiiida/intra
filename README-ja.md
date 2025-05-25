## Traefik リバースプロキシの設定

Traefik をリバースプロキシとして動作させるサービスを適切に設定するには、docker-compose ファイルで適切なネットワークとラベルを定義する必要があります。

### 必要なネットワーク

docker-compose ファイルで外部ネットワークを定義します。

```yml
networks:
  traefik_global:
    external: true
```

### サービス設定

Traefik で公開するサービスごとに、以下の設定を追加します。

```yml
services:
  your_service:
    # 既存サービスの設定...
    networks:
      - "traefik_global"
    labels:
      - traefik.enable=true
      - traefik.docker.network=traefik_global
      - traefik.http.routers.${YOUR_SERVICE}.rule=Host(`${YOUR_SERVICE_HOST}`)
      - traefik.http.routers.${YOUR_SERVICE}.service=${YOUR_SERVICE}
      - traefik.http.services.${YOUR_SERVICE}.loadbalancer.server.port=<port>
networks:
  traefik_global:
    external: true
```

`${VAULT_HOST}` を実際のドメインに置き換え、ポート番号がコンテナのweb公開ポートと一致していることを確認してください。

## OrbStack を使用したローカル開発

macOS でのローカル開発では、自動証明書管理機能を備えたローカルリバースプロキシとして [OrbStack](https://orbstack.dev/) を使用することもできます。

ローカルの `/etc/hosts` が正しく構成されていれば、どのドメインでも動作しますが、ローカル開発環境ではローカルドメインと内部ドメインの使用が推奨されます。

### OrbStack の設定

サービスに以下のラベルを追加するだけです。

```yml
services:
  your_service:
    # サービスの設定...
    # Your service configuration...
    labels:
      - dev.orbstack.domains=${YOUR-SERVICE}.local
```
