---
tags: 
---

### 説明
お問い合わせフォーム用コントローラー

### code

```php

<?php

namespace App\Http\Controllers;
  

use Illuminate\Http\Request;
use App\Models\Contact;
use App\Models\Category;
use App\Http\Requests\ContactRequest;
  

class ContactController extends Controller

{
    public function index(Request $request)

    {
        $contacts = Contact::with('category')->get();
        $categories = Category::all();
        $contact = $request->session()->get('contact_data', []);

        return view('contact', compact('categories', 'contacts', 'contact'));

    }

  
    //contact.blade.phpから送信された問い合わせフォームの内容をconfrim.blade.phpで表示

    public function confirm(ContactRequest $request)

    {

        $category = Category::find($request->input('category_id'));

        $tel = $request->input('tel1') . $request->input('tel2') . $request->input('tel3');

        $name = $request->input('last_name') . ' ' .   $request->input('first_name');

        $genderLabel = [

            1 => '男性',
            2 => '女性',
            3 => 'その他',

        ];

        $contact = $request->only([

            'category_id',
            'first_name',
            'last_name',
            'gender',
            'email',
            'address',
            'building',
            'detail',
        ]);

        $contact['tel'] = $tel;

        $contact['tel1'] = $request->input('tel1');

        $contact['tel2'] = $request->input('tel2');

        $contact['tel3'] = $request->input('tel3');

  

        // セッションに値を保存する

        // request->session()->put(キー, 値);

        $request->session()->put('contact_data', $contact);

        return view('confirm', compact('contact', 'category', 'genderLabel', 'name'));

    }

  
  

    //confirm.blade.phpから送信された問い合わせフォームの内容をDBへ保存

    public function store(Request $request)

    {

        // セッションからデータを取得

        $contact = $request->session()->get('contact_data');
  

        // もしセッションにデータがなければcontactページを表示してDB保存エラーを防ぐ

        if (!$contact) {

            return redirect('/contact')->with('error', 'セッションが切れました');

        }

        Contact::create($contact);
 

        //セッション削除
        $request->session()->forget('contact_data');

        return redirect('/thanks');

    }

}

