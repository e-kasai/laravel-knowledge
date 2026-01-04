
```php
<?php

namespace App\Http\Controllers
  

use Illuminate\Http\Request;

// 検索対象のモデルインポート

use App\Models\Contact;

  

class AdminController extends Controller

{

    public function index(Request $request)

    {

        // 初期表示は全件表示（最新順、7件ずつ）

        $contacts = Contact::orderBy('created_at', 'desc')->paginate(7);

  

        // -----------------検索機能-------------------------

  

        //複数の検索条件を扱うのでqueryビルダーを用意する

        $query = Contact::query();

  

        // キーワード（姓、名、メールアドレスでの検索）

        if ($request->filled('keyword')) {

            $keyword = trim($request->keyword);

            $query->where(function ($q) use ($keyword) {

                $q->where('last_name', 'LIKE', "%{$keyword}%")

                    ->orWhere('first_name', 'LIKE', "%{$keyword}%")

                    ->orWhere('email', 'LIKE', "%{$keyword}%")

                    // 重要：?でバインドしインジェクションを防止する

                    ->orWhereRaw(

                        "CONCAT(last_name, ' ', first_name) LIKE ?",

                        ["%{$keyword}%"]

                    )

                    ->orWhereRaw(

                        "CONCAT(last_name, first_name) LIKE ?",

                        ["%{$keyword}%"]

                    );

            });

        }

        //性別で検索（値がある場合のみフィルタ、nullまたは0は全件表示）

        if (!empty($request->gender) && $request->gender !== '0') {

            $query->where('gender', $request->gender);

        }

  

        //お問い合わせカテゴリで検索（値がある場合のみフィルタ、nullまたは0は全件表示）

        if (!empty($request->category_id) && $request->category_id !== '0') {

            $query->where('category_id', $request->category_id);

        }

  

        //日付で検索

        if ($request->filled('date')) {

            $query->whereDate('created_at', $request->date);

        }

  

        // 検索結果を取得（ページネーションで１ページは７件まで）

        $contacts = $query->with('category')->orderBy('created_at', 'desc')->paginate(7)->withQueryString();

        return view('admin', compact('contacts'));

    }

  
  

    // ----------------管理画面から検索結果をCSVエクスポート-------------------

    public function export(Request $request)

    {

        //複数の検索条件を扱うのでqueryビルダーを用意する

        $query = Contact::query();

  

        // indexと同じ検索条件を使用し値を取得

        // キーワード（姓、名、フルネーム、メールアドレスで検索）

        if ($request->filled('keyword')) {

            $keyword = trim($request->keyword);

            $query->where(function ($q) use ($keyword) {

                $q->where('last_name', 'LIKE', "%{$keyword}%")

                    ->orWhere('first_name', 'LIKE', "%{$keyword}%")

                    ->orWhere('email', 'LIKE', "%{$keyword}%")

                    // 重要：?でバインドしインジェクションを防止する

                    ->orWhereRaw(

                        "CONCAT(last_name, ' ', first_name) LIKE ?",

                        ["%{$keyword}%"]

                    )

                    ->orWhereRaw(

                        "CONCAT(last_name, first_name) LIKE ?",

                        ["%{$keyword}%"]

                    );

            });

        }

        //性別で検索（値がある場合のみフィルタ、nullまたは0は全件表示）

        if (!empty($request->gender) && $request->gender !== '0') {

            $query->where('gender', $request->gender);

        }

  

        //お問い合わせカテゴリで検索（値がある場合のみフィルタ、nullまたは0は全件表示）

        if (!empty($request->category_id) && $request->category_id !== '0') {

            $query->where('category_id', $request->category_id);

        }

  

        //日付で検索

        if ($request->filled('date')) {

            $query->whereDate('created_at', $request->date);

        }

  

        //全件取得

        //csv出力時お問い合わせの種類をidでなくcontentで表示するのでwith

        $contacts = $query->with('category')->get();

  

        //<------------CSVエクスポート---------->

  

        //ファイル名 （イメージ: contacts_20250719_123456.csv）

        //上書きなどファイル重複防止のためにHisの形式を使用

        $filename = 'contacts_' . now()->format('Ymd_His') . '.csv';

  

        //無名関数外の$contactをuseで持ち込みしファイルをwriteモードで開く

        $callback = function () use ($contacts) {

            $handle = fopen('php://output', 'w');

  

            //OSによる文字化けを防止する

            fputs($handle, "\xEF\xBB\xBF");

  

            //csvファイルのヘッダー

            fputcsv($handle, ['氏名', '性別', 'メール', 'お問い合わせの種類', '登録日時']);

            //検索結果または全件取得結果

            foreach ($contacts as $contact) {

                fputcsv($handle, [

                    $contact->last_name . ' ' . $contact->first_name,

                    $contact->gender == 1 ? '男性' : ($contact->gender == 2 ? '女性' : 'その他'),

                    $contact->email,

                    $contact->category->content,

                    $contact->created_at->format('Y-m-d'),

                ]);

            }

            //php://output(ブラウザに直接出力する仮想ファイル)を閉じる

            fclose($handle);

        };

        // ResponseFactoryを呼び出してその中のインスタンスメソッドstreamDownloadを呼び出す

        return response()->streamDownload($callback, $filename, [

            'Content-Type' => 'text/csv',

        ]);

    }

  
  
  

    // -----------------管理画面から削除する機能--------------------------------

    public function destroy($id)

    {

        $contact = Contact::findOrFail($id);

        $contact->delete();

        //直前のページ（削除ボタンを押したときのURL）にredirect

        return redirect()->back();

    }

}
```