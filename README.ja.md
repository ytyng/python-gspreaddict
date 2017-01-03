# gspreaddict


Google スプレッドシートを OrderedDict のリストとして扱います。読み込みのみ。

スプレッドシートの1行目を、OrderdDictのキーとして扱います。

Django のキャッシュなどを使い、リクエストをキャッシュすることができます。


## 事前準備

### Google スプレッドシートで認証情報を作っておく

Drive api ダッシュボードにアクセス

https://console.developers.google.com/apis/dashboard

認証情報 → 認証情報を作成 → サービスアカウントキー

JSON形式でダウンロードしておく


### 読み込むスプレッドシートに、先程の認証情報を認可する

Google スプレッドシートを開く。

スプレッドシートの共有ボタンをクリックし、先程の JSON に含まれている client_email

xxxxxxxxxxxx@appspot.gserviceaccount.com

を入力し、完了。


## 使い方

    from gspreaddict import GSpreadDict

    class TestRecord(GSpreadDict):
        spreadsheet_key = 'xxxxxxxxxxxxxxxxxxxxxxxxxxxx'
        credentials_json_path = os.path.join(
            os.path.dirname(__file__), "My Project-xxxxxxxx.json")

spreadsheet_key は、GoogleスプレッドシートのURLに含まれるIDの文字列。
credentials_json_path は、ダウンロードした認証情報にアクセスするパス。

    record = TestRecord.objects.get(key=value))

とか

    record = TestRecord.objects.get(lambda x: x['key'] == value)

のようにして、OrderedDict の子クラスのインスタンスを作れます。


## キャッシング

    from django.core.cache import caches

    class TestRecord(GSpreadDict):
        spreadsheet_key = 'xxxxxxxxxxxxxxxxxxxxxxxxxxxx'
        credentials_json_path = os.path.join(
            os.path.dirname(__file__), "My Project-xxxxxxxx.json")

    @classmethod
    def cache_set(cls, key, value):
        caches['gspreaddict'].set(key, value)

    @classmethod
    def cache_get(cls, key):
        return caches['gspreaddict'].get(key)

cache_set, cache_get をオーバーライドすることで、
Google へのアクセスをキャッシュすることができます。
