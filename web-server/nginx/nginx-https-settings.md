# nginx https設定

## Let's encrypt

https 可以使用免費的 Let’s Encrypt，在 https 安裝上推薦使用憑證機器人自動更新 Let’s Encrypt 憑證。

```bash
sudo certbot --nginx -d test.domain.com
sudo certbot delete --cert-name test.domain.com
sudo certbot renew --dry-run
```

## 參考資料

* [\[digital ocean\] How To Secure Nginx with Let's Encrypt on Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-secure-nginx-with-let-s-encrypt-on-ubuntu-16-04)。
