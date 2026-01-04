---
tags: #template #migraion #外部キー #template #テンプレ
---

#### 説明
外部キーのあるmigration fileのテンプレ

#### code

```php

<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateContactsTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('contacts', function (Blueprint $table) {
            $table->id();
            // 外部キーの読み込み
            // constrained('categories')を明示することでcategories.id を参照する
            // onDelete('cascade') 親が消えたら属する子も削除する
            $table->foreignId('category_id')->constrained('categories')->onDelete('cascade');
            $table->string('first_name', 255);
            $table->string('last_name', 255);
            
            $table->tinyInteger('gender')->unsigned();
            $table->string('email', 255);
            $table->string('tel', 255);
            $table->string('address', 255);
            $table->string('building', 255)->nullable();
            $table->text('detail');
            $table->timestamps();
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::dropIfExists('contacts');
    }
}

```