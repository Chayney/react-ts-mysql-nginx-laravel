# React×TypeScript×MySQL×Nginx×Laravel  
※環境構築がメインです。  
Docker環境で構築しフロントエンドはReact、バックエンドはLaravelでAPIを取得しています。  

## Docker内での接続設定  
Docker内で起動しているViteは、デフォルトでは localhost(127.0.0.1)からのアクセスのみ  
vite.confug.ts  
plugins: [react()],  
  server: {  
    host: '0.0.0.0',  
    port: 5173  
  },  

## Laravel Sanctum SPA認証 
### Sanctumのインストール
$docker-compose exec php bash  
composer require laravel/sanctum  
php artisan vendor:publish --provider="Laravel\Sanctum\SanctumServiceProvider"  
php artisan migrate  

### Sanctumのセットアップ  
config/sanctum.phpで確認  
※Reactはviteで構築  
'stateful' => explode(',', env('SANCTUM_STATEFUL_DOMAINS', 'localhost,localhost:5173')),  

.envで追加  
SANCTUM_STATEFUL_DOMAINS=localhost:5173  
SESSION_DOMAIN=localhost  

.envの編集後は自動的に編集内容が反映しないのでキャッシュをクリア  
php artisan config:clear  
php artisan cache:clear  

app/Http/Kernel.php  
'api' => [  
\Laravel\Sanctum\Http\Middleware\EnsureFrontendRequestsAreStateful::class,  
'throttle:api',  
\Illuminate\Routing\Middleware\SubstituteBindings::class,  
],  

config/cors.php  
'paths' => ['api/*', 'sanctum/csrf-cookie'],  
'allowed_origins' => ['http://localhost:5173'], // CORSの仕様上、'supports_credentials'をtrueにするとここでワイルドカードは使用出来ない。接続先の明示が必要  
'allowed_methods' => ['*'],  
'allowed_headers' => ['*'],  
'supports_credentials' => true, // cookieなどの認証情報を許可  

## 接続先
React http://localhost:5173  
Laravel http://localhost  
phpMyAdmin http://localhost:8080  

## ページURL  
登録画面 http://localhost:5173/register  
ログイン画面  http://localhost:5173/login  
TOP画面  http://localhost:5173 // ログインしていなければログイン画面にリダイレクト
