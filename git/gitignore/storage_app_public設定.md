
---

1. src/storage/app/.gitignore
```php
*             #このsrc/storage/app以下をすべてignore
!public/      #publicフォルダ除く(中身はignore)
!.gitignore   #この.gitignoreファイルは無視しない
```

![[Pasted image 20250929094112.png]]


---
2. src/storage/app/public

public内の任意のディレクトリの.gitkeepだけ残す
→アップロード画像などはgitに上げない設定
```php
*                             #app/public以下をすべてignore
!.gitignore                #この.gitignoreファイルは無視しない

!material_images/             #アップロード画像の入った"フォルダ自体"は残す（フォルダ名は例、中身はignore）
!material_images/.gitkeep     #中身はignoreするが、フォルダ自体を残すための".gitkeep"はignoreしない


```

![[Pasted image 20250929094042.png]]

---

3. 各ディレクトリ内に.gitkeep設置しフォルダ自体を残す
![[Pasted image 20250929101054.png]]