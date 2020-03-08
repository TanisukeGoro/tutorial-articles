# Laravelのローカル環境構築について  
取り敢えず初心者は`Homestead`やっとけと。    
    
### 参考文献    
[【Laravel超入門】開発環境の構築（VirtualBox + Vagrant + Homestead + Composer） - Qiita](https://qiita.com/7968/items/97dd634608f37892b18a)    
[Laravel Homestead 5.8 Laravel](https://readouble.com/laravel/5.5/ja/homestead.html)    
    
必要なのは以下    
    
- Vagrant    
- Virtualbox    
    
`Vagrant`は必須。用意した仮想環境を操作するのに超絶便利なソフト。    
`Virtualbox`は仮想環境。VMWareと同じで他にも色々あるけど、Virtualboxがよく使われているようなのでこれで行く。    
    
## Vagrantのインストール    
    
普通にサイトにアクセスしてインストールすべし。セキュリティの警告出るけどそのままやる。    
    
[Download - Vagrant by HashiCorp](https://www.vagrantup.com/downloads.html)    
    
## Virtualboxのインストール    
    
同上    
    
[Downloads – Oracle VM VirtualBox](https://www.virtualbox.org/wiki/Downloads)    
    
## Homestead Vagrant Box のインストール    
`Vagrant`と`Virtualbox`がインストールできたら`Homestead`をインストールする    
ターミナルを立ち上げて以下のコマンドを実行    
    
```bash  
vagrant box add laravel/homestead  
# 使用するVMソフトを聞かれるので、番号で指定  
# 1) hyperv  
# 2) parallels  
# 3) virtualbox  
# 4) vmware_desktop  
  
# Enter your choice: 3 <= virtualboxなので3  
```  
    
インストールに10分とかかかるので気長にまつ    
    
## Homesteadのインストール  
    
Githubから`Homestead`をcloneしてやる。    
自分のホームディレクトリに`Homestead`フォルダを作成してクローンするのは、自分の`Laravel`の全プロジェクトをホストしておくHomestead Boxを用意することと同じ。    
    
```bash    
# 以下のコマンドで [ ~/Homestead ] にクローン    
git clone https://github.com/laravel/homestead.git ~/Homestead    
  
# masterは常に安定しているわけではないので、バージョンタグがついた  
# Homesteadを利用するべき。  
# なのでHomesteadのディレクトリに移って  
cd ~/Homestead  
  
# 以下のようにチェックアウト  
git checkout release  
  
```    
Homesteadリポジトリをクローンしたら、`Homestead.yaml`設定ファイル(YAMLファイル)を生成するために、`bash init.sh`コマンドを`Homesteadディレクトリ`で実行します。    
    
```bash    
# 念のため    
cd ~/Homestead    
# Mac / Linux...    
bash init.sh    
# Windows...    
init.bat    
    
```    
    
    
### YAMLとは？    
    
YAML(ヤムル・ヤメル)ファイルと呼ぶ。    
    
>    
> YAML は 「YAML Ain't a Markup Language」 の略で 「YAMLはマークアップ言語ではない」 という意味。    
> HTML（HyperText Markup Language）のようなマークアップ言語ではないということですね。    
>    
> YAMLファイルの拡張は yml または yaml です。    
> YAML は 主に設定ファイル を記述するのに用いられる言語です。    
> YAML には 読みやすい、書きやすい、わかりやすい という特徴があります。    
> 例えば、配列を表すには先頭に - を入力するだけです。    
>    
    
まあ、markdown的な感じでかける設定ファイルなんだと思う。    
    
### SSH鍵ファイルの作成    
ホストOSとゲストOSとのやりとりのためにSSH通信を行う。そのためSSHでやりとりするための鍵を作成する。    
SSH鍵があるかホームディレクトリで確認    
    
```    
cd ~/    
ls -la I grep .ssh    
# ls: I: No such file or directory    
# ls: grep: No such file or directory    
# .ssh:    
# total 24    
# drwx------   5 abekeishi  staff   160 10  1  2018 ./    
# drwxr-xr-x@ 75 abekeishi  staff  2400  6 27 00:07 ../    
# -rw-------@  1 abekeishi  staff  1766 10  1  2018 id_rsa    
# -rw-r--r--@  1 abekeishi  staff   401 10  1  2018 id_rsa.pub    
# -rw-r--r--@  1 abekeishi  staff  1175  6  4 10:47 known_hosts    
    
```    
こんな感じで`id_rsa`, `id_rsa.pub`があればOK。なければ作るべし。    
    
> 作り方は参考文献(qiita)を参照    
>     
    
## Homesteadの設定ファイルの編集    
    
Homesteadに関する設定を行う。    
Homesteadディレクトリで`Homestead.yaml`を編集。    
    
```    
cd ~/Homestead    
vim Homestead.yaml    
```    
    
設定は基本的に`キー : 値`といった形で設定する。    
    
### provider キー    
    
Vagrantのプロバイダとしてどの仮想環境を実行するか指定する。    
`virtualbox`、`vmware_fusion`、`vmware_workstation`、`parallels`、`hyperv`のいずれかを指定    
    
```    
provider : virtualbox    
```    
    
### folders キー (共有フォルダの設定)    
    
多分これが一番大事。    
Homestead環境と共有したい全フォルダがリストされる。    
これらのフォルダの中のファイルが変更されるとローカルマシンとHomestead環境との間で同期される。    
必要なだけ、必要な分だけ共有フォルダを設定すること。    
    
少数のサイトを作るだけなら以下のままでまあ大丈夫。    
`map`がホストOS(元のやつ)のディレクトリで、`to`がゲストOSディレクトリ。    
    
    
```    
folders :     
    - map : ~/code    
      to  : /home/vagrant/code    
```    
    
ただ、多くのサイトが継続的に成長して行くにつれてバフォーマンスに問題が生じる。    
>     
> この問題は’とても大きいファイルを含むローエンドのマシンやプロジェクトで、悲痛なほど顕著に現れます。この問題が起きたら、全プロジェクトを自身のVagrantフォルダにマップしてください。    
> [Laravel Homestead 5.8 Laravel](https://readouble.com/laravel/5.8/ja/homestead.html)    
>     
    
ということでこんな感じでやる。    
```    
folders:    
    - map: ~/code/project1    
      to: /home/vagrant/code/project1    
    
    - map: ~/code/project2    
      to: /home/vagrant/code/project2    
```    
    
### sites キー (ローカルのブラウザでみる用)    
ローカルのブラウザでもデバッグが出来るように設定する必要がある。    
`map`は基本そのまんまでいいと思う。    
`to`はHomestaadの`index.php`があるディレクトリを指定すること。    
変更したら`vagrant reload --provision`を叩く。    
    
```    
sites:    
    - map: homestead.test    
      to: /home/vagrant/code/cms/public    
          
```    
    
```    
# これで通信の許可を与えておく。    
sudo vi /etc/hosts    
```    
これで    
    
    
    
    
## 設定が終わったら仮想マシンの起動    
    
    
```    
# homestead ディレクトリに移動して    
cd ~/Homestead    
# vagrant を起動。仮想マシンを起動する。    
vagrant up    
```    
    
ちゃんと起動すればvirtualboxのソフトを起動すると、`homestead-7`との表示が出てくる。    
    
### vagrantの操作    
vagrant は`~/Homestead`のディレクトリでコマンドを実行すること    
#### 立ち上げ    
`vagrant up`    
    
#### 消去    
`vagrant destroy`    
    
#### 一時停止    
`vagrant suspend`    
    
#### suspendからの復帰    
`vagrant resume`    
    
#### 現状確認    
`vagrant status`    
    
#### vagrantfileの再起動    
`vagrant reload`    
    
    
#### 仮想環境での操作    
`homestead`が起動したらまずは`ホストOS`のターミナルから`ゲストOS`にログインしてみる    
`vagrant ssh`    
これで入れるはず。    
    
    
    
# Homesteadを使ってlaravelのチュートリアルをやる    
これでようやくHomesteadでlaravelの開発ができるようになりました。    
山崎先生のチュートリアルを参考にlaravelの開発を進めてみましょう。    
    
この先では基本的に`vagrant ssh`でログインした仮想環境上でコマンドを打ちます。    
ログインしたそのままの状態では`ホームディレクトリ`にいるので作成したプロジェクトに移動してからコマンドを叩くこと。    
    
また特別な断りがない限りは仮想環境上のターミナルを操作することに留意すべし。    
    
## laravelのプロジェクト作成    
    
`cms`というプロジェクトを作成する    
    
```bash     
cd code    
composer create-project laravel/laravel cms 5.5.* --prefer-dist    
# プロジェクトができたらディレクトリに移動    
cd cms    
```    
    
## phpMyAdminの設定    
別になくてもいいけど、あれば便利なので    
    
```bash    
cd public   # publicフォルダに移動
wget https://files.phpmyadmin.net/phpMyAdmin/4.8.3/phpMyAdmin-4.8.3-all-languages.zip    
unzip phpMyAdmin-4.8.3-all-languages.zip
rm phpMyAdmin-4.8.3-all-languages.zip # いらないので消す   
mv phpMyAdmin-4.8.3-all-languages phpMyAdmin # 名前を変更
```    
`public`フォルダのなかに`phpMyAdmin-4.8.3-all-languages`フォルダができるので`phpMyAdmin`に変更する。インストールに使った`zip`ファイルも消して良い    
    
ブラウザでデバッグしているページのURLの最後に`/phpMyAdmin/index.php`と入力してみるとphpMyAdminが開けるはず。    
> 参考URL : `http://192.168.10.10/phpMyAdmin/index.php`    
    
ユーザー名とpasswordは`cms`ディレクトリに移動して`cat .env`をする。    
そして中にある`DB_USERNAME`と`DB_PASSWORD`の値を入力してログインできるはず。    
    
ちなみに以下のようになっている    
    
```    
DB_CONNECTION=mysql    
DB_HOST=127.0.0.1    
DB_PORT=3306    
DB_DATABASE=homestead    
DB_USERNAME=homestead    
DB_PASSWORD=secret    
```    
    
この辺の項目にDBの文字コード設定などを書くといいようだけどよくわからんのでこの辺は高井さんに聞くかね。    
    
    
## git管理    
ここで一旦 git initしてコミットすることをオススメする。    
色々コマンド(php atisanなど)を打って変更を加えるが、いまいち何が変わっているのかわかりづらい。    
gitで変更を追えるようにしておくと、入力したコマンドがどのファイルにどんな変更を加えたのか非常に分かりやすくなる。    
さらにうっかりコマンドをミスっても即座に巻き戻せるのだ！！ね。便利でしょ。    
gitの操作は基本的に`ローカルのターミナル`でやるといい。    
    
    
また`.gitignore`に`.DS_Store`を追加しているといいよ。    
    
    
```bash    
# ローカルのターミナルで    
cd ~/code/cms    
git init    
git add .    
git commit -m'🎉 init commit'    
```    
    
    
## ログイン認証の実装    
ログイン機能は先にやってしまった方がいい気がする。    
自分で`resources/view/layouts`フォルダとか作るの面倒だし    
    
/--------------------------------------------    
/.認証画面作成（make:auth）     
/--------------------------------------------    
```    
php artisan make:auth    
# [yes]Enter    
php artisan migrate    
``````    
    
これで`/resouces/views/auth/`, `/resouces/views/layouts/`が追加されているはず    
    
    
## Booksテーブルの作成    
1. booksテーブルを作成する    
    
```    
php artisan make:migration create_books_table --create=books    
```    
/--------------------------------------------    
/２．[年]_[月]_[日]_[時分秒]_create_books_table.php    
/   public function up(){...}の中を追加修正    
/--------------------------------------------    
```    
public function up()    
    {    
        Schema::create('books', function (Blueprint $table) {    
            $table->increments('id');    
            $table->string('item_name');    
            $table->integer('item_number');    
            $table->integer('item_amount');    
            $table->datetime('published');    
            $table->timestamps();    
        });    
}    
```    
    
2. Table作成するスクリプト準備 完了    
    
    
/--------------------------------------------    
/３．マイグレーションを実行（テーブル作成）    
/--------------------------------------------    
以下を実行    
```    
php artisan migrate    
```    
    
web.phpへのルートの定義はまだしない    
    
## Booksのページを作成    
    
/--------------------------------------------    
/１．/resources/views/books.blade.php を作成    
/   78ページ    
/   以下コードを貼り付けます。    
/--------------------------------------------    
1. 以下を全てのコードをコピー    
    
```html    
<!-- resources/views/books.blade.php -->    
@extends('layouts.app')    
@section('content')    
    <!-- Bootstrapの定形コード… -->    
    <div class="card-body">    
        <div class="card-title">    
            本のタイトル    
        </div>    
            
        <!-- バリデーションエラーの表示に使用-->    
    	@include('common.errors')    
        <!-- バリデーションエラーの表示に使用-->    
    
        <!-- 本登録フォーム -->    
        <form action="{{ url('books') }}" method="POST" class="form-horizontal">    
            {{ csrf_field() }}    
    
            <!-- 本のタイトル -->    
            <div class="form-group">    
                <div class="col-sm-6">    
                    <input type="text" name="item_name" class="form-control">    
                </div>    
            </div>    
    
            <!-- 本 登録ボタン -->    
            <div class="form-group">    
                <div class="col-sm-offset-3 col-sm-6">    
                    <button type="submit" class="btn btn-primary">    
                        Save    
                    </button>    
                </div>    
            </div>    
        </form>    
    </div>    
    <!-- Book: 既に登録されてる本のリスト -->    
    
@endsection    
    
```    
/--------------------------------------------    
/ ３．/resources/views/common/errors.blade.php を作成     
/ 81ページ    
/ 以下コードを貼り付けます。    
/--------------------------------------------    
1. 以下をコピー    
    
```html    
<!-- resources/views/common/errors.blade.php -->    
@if (count($errors) > 0)    
    <!-- Form Error List -->    
    <div class="alert alert-danger">    
        <div><strong>入力した文字を修正してください。</strong></div>     
        <div>    
            <ul>    
            @foreach ($errors->all() as $error)    
                <li>{{ $error }}</li>    
            @endforeach    
            </ul>    
        </div>    
    </div>    
@endif    
```    
    
## Booksのルートを定義(ルーティング)    
    
以下のコードを`routes/web.php`から削除する    
```javascript    
    
Route::get('/', function () {    
    return view('welcome');    
});    
    
```    
    
    
以下のコードを`routes/web.php`に入力する。    
    
    
```javascript    
    
use Illuminate\Http\Request;    
use App\Book;    
    
/**    
 * 本のダッシュボード表示(books.blade.php)    
 */    
Route::get('/', function () {    
    $books = Book::orderBy('created_at', 'asc')->get();    
    return view('books', [    
        'books' => $books    
    ]);    
    //return view('books',compact('books')); //も同じ意味    
});    
/**    
 * 新「本」を追加     
 */    
Route::post('/books', function (Request $request) {    
    //    
});    
    
/**    
 * 本を削除     
 */    
Route::delete('/book/{book}', function (Book $book) {    
    //    
});    
    
    
Route::post('/books', function (Request $request) {    
    
    //バリデーション    
    $validator = Validator::make($request->all(), [    
        'item_name' => 'required|max:255',    
    ]);    
    
    //バリデーション:エラー     
    if ($validator->fails()) {    
        return redirect('/')    
            ->withInput()    
            ->withErrors($validator);  
    }  
    //以下に登録処理を記述（Eloquentモデル）  
    // Eloquent モデル  
    $books = new Book;  
    $books->item_name = $request->item_name;  
    $books->item_number = '1';  
    $books->item_amount = '1000';  
    $books->published = '2017-03-07 00:00:00';  
    $books->save();  
    return redirect('/');  
});  
  
```  
  
## 再びbooksのページ編集  
`books.blade.php`の`</form>`タグの下に以下を貼り付ける  
  
```html  
    <!-- 現在の本 -->  
    @if (count($books) > 0)  
    <div class="card-body">  
        <div class="card-body">  
            <table class="table table-striped task-table">  
                <!-- テーブルヘッダ -->  
                <thead>  
                    <th>本一覧</th>  
                    <th>&nbsp;</th>  
                </thead>  
                <!-- テーブル本体 -->  
                <tbody>  
                    @foreach ($books as $book)  
                    <tr>  
                        <!-- 本タイトル -->  
                        <td class="table-text">  
                            <div>{{ $book->item_name }}</div>  
                        </td>  
  
                        <!-- 本: 削除ボタン -->  
                        <td>  
                            <form action="{{ url('book/'.$book->id) }}" method="POST">  
                                {{ csrf_field() }}  
                                {{ method_field('DELETE') }}  
                                <button type="submit" class="btn btn-danger">  
                                    削除  
                                </button>  
                            </form>  
                        </td>  
                    </tr>  
                    @endforeach  
                </tbody>  
            </table>  
        </div>  
    </div>  
    @endif  
```  
  
  
## またまたルーティング設定  
`web.php`の最後の方に以下をコピー  
  
```javascript  
Route::delete('/book/{book}', function (Book $book) {  
    $book->delete();       //追加  
    return redirect('/');  //追加  
});  
  
```  
  
## bootstrapを読み込む  
  
`app.blade.php`に以下をコピペ  
  
```  
<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css" integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">  
```  
  
# laravelプロジェクトをGit cloneした後の処理
laravelのプロジェクトでは`.gitignore`が自動で生成され、個々の環境で作る必要のあるファイルやフォルダはそこに登録されている。
したがって`git clone`した後にちゃんと環境を整え直す必要がある。

1. `composer update`  
    これは仮想環境のターミナル上でプロジェクトのディレクトリで実行する
2. `.env.example`をコピーしコピーしたファイルを`.env`に書き換える
3. `php artisan key:genarate`を実行。keyが生成されたらおっけい




 githubからcloneするときは
<!--  
Laredok  
Laravel  
  
## 仮想環境( VM )  
元のOS(私ならMac)上で別環境としてOSなどを実行できる環境  
VirtualBox  
VMWare  
Parallels  
Hyper-V  
  
  
## Vagrant  
いろんなVM(仮想環境)を動かす仮想化ソフトの超すごいラッパーツール  
これはVMソフトのソフトを代行  
  
## Docker  
仮想環境とは違うけど役割としては似たようなもの  
-->

# homesteadに初期のDBをCSVで構築する方法(特に巨大なデータ)
pythonでカラムが40、rowが5万近いデータベースをCSVで作成し、MySQLで登録しようとした時に詰まったので。
​
>
> 以下を参考に我流でやってるので正しいのかはわかりません。
>
> [データベース：マイグレーション 5.5 Laravel](https://readouble.com/laravel/5.5/ja/migrations.html)  
> [データベース：シーディング 5.5 Laravel](https://readouble.com/laravel/5.5/ja/seeding.html)  
> [LaravelでユニットテストのためのデータベースデータをCSVファイルだけで準備する - Qiita](https://qiita.com/kd9951/items/52138ced734646da7019)  
> [【Laravel】CSVファイルのデータをSeederでDBにつっこむ - Qiita](https://qiita.com/shosho/items/caf535b1df5cb8d8ae92)  
> 
​
まずはテーブルの作成
```
 php artisan make:migration create_category_info --create=category_infos
```
​
## テーブルの定義を指定
コマンドがうまく実行されると、`20XX_XX_XX_create_category_info.php`が`database/migrations/`以下に作成されるはず。  
作成されたファイルの以下を変更する  
​
```php
<?php
​
use Illuminate\Support\Facades\Schema;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Database\Migrations\Migration;
​
class CreateCategoryInfo extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('category_infos', function (Blueprint $table) {
            $table->integer('business_category');
            $table->string('business_category_name');
            $table->timestamps();
        });
    }
​
    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::dropIfExists('category_infos');
    }
}
​
​
```
​
​
CVSをデータベースに追加するためのライブラリをインポート
```
composer require --dev flynsarmy/csv-seeder
```
​
​
## Seederを作成する(シーダクラス定義)
​
個人的には`テーブル名+Seeder`としている  
```
php artisan make:seeder CompanyInfoSeeder
```
​
すると以下のようなファイルが生成されるはず。  
​
```php
<?php
​
use Illuminate\Database\Seeder;
​
class CategoryInfoSeeder extends Seeder
{
    /**
     * Run the database seeds.
     *
     * @return void
     */
    public function run()
    {
        // ここにテーブルを挿入る文章をかく
    }
}
​
```
​
## SeederにCSVを挿入するための文をかく
​
​
```php
​
<?php
​
use Illuminate\Database\Seeder;
​
class CategoryInfoSeeder extends Seeder
{
    /**
     * Run the database seeds.
     *
     * @return void
     */
    public function run()
    {
        // databaseフォルダの直下にcsv_dbを作成してその中にいくつかのcsvを作成した。
        $file = new SplFileObject('database/csv_db/XXX.csv'); 
        
​
        // これがCSVを読み込む処理。基本そのまま
        $file->setFlags(
            \SplFileObject::READ_CSV |
                \SplFileObject::READ_AHEAD |
                \SplFileObject::SKIP_EMPTY |
                \SplFileObject::DROP_NEW_LINE
        );
​
        $list = [];
        $date = new DateTime();
        $now = $date->format('Y-m-d');
        // この中にCSVのデータとテーブルのカラム名を紐付ける
        foreach ($file as $line) {
            $list[] = [
                // "カラム名" => $line[x]  // x は カラムに入れるデータがcsvの何列目か指定
                "business_category" => intval($line[0]),
                // "business_category" => floatval($line[0], 6,3),float型で指定することもできる
                "business_category_name" => $line[1],
                "created_at" => $now,
                "updated_at" => $now
            ];
        }
        // DB::table("category_infos")->insert($data); // データの規模が大きくなければそのまま$dataを突っ込んでいい
​
        // データが巨大な場合は配列を分割して挿入していく
        $chunk_size = 1000;
        $chunk_data = array_chunk($list, $chunk_size);
​
        foreach ($chunk_data as $data) {
            DB::table("category_infos")->insert($data);
        }
        //ここまで
    }
}
​
```
​
​
### シーダの呼び出し
`database/seeds/DatabaseSeeder.php`にあるところに作成したSeederクラスを追加して呼び出せるようにする
​
​
```php
<?php
​
use Illuminate\Database\Seeder;
​
class DatabaseSeeder extends Seeder
{
    /**
     * Run the database seeds.
     *
     * @return void
     */
    public function run()
    {
        $this->call(CompanyInfoSeeder::class);
        $this->call(FinancialReportsSeeder::class);
        $this->call(CategoryInfoSeeder::class);
    }
}
​
```
​
## テーブルの作成
リフレッシュしてseederを実行する   
```
php artisan migrate:fresh
php artisan db:seed
```

# bootstrap3からbootstrap4へ移行する方法
[Laravel5.5インストールからBootstrap4を導入するまで - Qiita](https://qiita.com/hondy12345/items/fef482c347b883acff84)

[Bootstrap 4 Installation With Laravel 5.7 - Stack Overflow](https://stackoverflow.com/questions/53752401/bootstrap-4-installation-with-laravel-5-7)

[Laravel 5.5 で Bootstrap 4 を使う \| iwasan.net](https://iwasan.net/laravel-5-5-%E3%81%A7-bootstrap-4-%E3%82%92%E4%BD%BF%E3%81%86/)

[アセットのコンパイル(Laravel Mix) 5.4 Laravel](https://readouble.com/laravel/5.4/ja/mix.html)

## 注意！
bootstrapの変更ではcssやjsの再ビルドが必要である。
`npm run dev`をすること。
そしてビルド時にbootstrapのモジュールをうまく読み込めない場合は以下の記事を参考にして見るといい。

[Laravel5.5 Bootstrap4のSassを使ってみる](https://qiita.com/n-oota/items/25601d0ab268461e66ac_