!!! warning "Wichtig"
    Lesen Sie zuerst [die Übersicht](r_p.md).

!!! warning "Warnung"
    Dies ist ein nicht unterstützter Community Beitrag. Korrekturen sind immer erwünscht!

Dieses Beispiel leitet den gesamten HTTP-Verkehr auf HTTPS um, mit Ausnahme des eingebauten ACME-Clients von mailcow.
Wenn Sie den eingebauten ACME-Client nicht verwenden möchten, ändern Sie bitte die Konfiguration selbst.

```
frontend https-in
  bind :::80 v4v6
  bind :::443 v4v6 ssl crt mailcow.pem

  acl mailcow_acme path -i -m beg /.well-known/

  redirect scheme https unless { ssl_fc || mailcow_acme }

  default_backend mailcow

backend mailcow
  option forwardfor
  http-request set-header X-Forwarded-Proto https if { ssl_fc }
  http-request set-header X-Forwarded-Proto http if !{ ssl_fc }
  server mailcow 127.0.0.1:8080 check
```
