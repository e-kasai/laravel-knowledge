
---

1. src/storage/framework下の設定

src/storage/framework/.gitignore
```
compiled.php
config.php
down
events.scanned.php
maintenance.php
routes.php
routes.scanned.php
schedule-*
services.json
```

2. storage/各サブディレクトリに.gitignore　(すでに自分でやらなくても設置されている)

src/storage/app/.gitignore
```
*
!public/
!.gitignore
```

src/storage/framework/cache/.gitignore
```
*
!data/
!.gitignore
```

src/storage/framework/sessions/.gitignore
```
*
!.gitignore
```

src/storage/framework/testing/.gitignore
```
*
!.gitignore

```

src/storage/framework/views/.gitignore
```
*
!.gitignore

```

src/storage/logs/.gitignore
```
*
!.gitignore
```


要するに以下の設定↓が個別にされている状態

src/storage/
├── app/
│   └── .gitignore
├── framework/
│   ├── cache/.gitignore
│   ├── sessions/.gitignore
│   └── views/.gitignore
└── logs/.gitignore