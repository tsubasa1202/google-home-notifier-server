# Google Homeの喋らせ方
必要なモジュールをインストール
```
npm install
```
サーバーを好きなポートで起動（デフォルト設定は8009）
```
node main.js
```
ローカルのポートとngrokのサーバーが立ち上がる  
テスト実行(ブラウザから)
```
https://<ngrokの自動生成されるサブドメイン>.ngrok.io/google-home-notifier?text=テストだよ
```
テスト実行(POST)
```
curl -i -X POST \
   -H "Content-Type:application/x-www-form-urlencoded" \
   -d \
'text=テストだよ' \
 'http://<ngrokの自動生成されるサブドメイン>.ngrok.io/google-home-notifier'
 ```

# google-home-notifier
Send notifications to Google Home

#### Installation
```sh
$ npm install google-home-notifier
```

#### Usage
```javascript
var googlehome = require('google-home-notifier');
var language = 'pl'; // if not set 'us' language will be used

googlehome.device('Google Home', language); // Change to your Google Home name
// or if you know your Google Home IP
// googlehome.ip('192.168.1.20', language);

googlehome.notify('Hey Foo', function(res) {
  console.log(res);
});
```

#### Listener
If you want to run a listener, take a look at the example.js file. You can run this from a Raspberry Pi, pc or mac. 
The example uses ngrok so the server can be reached from outside your network. 
I tested with ifttt.com Maker channel and it worked like a charm.

```sh
$ git clone https://github.com/noelportugal/google-home-notifier
$ cd google-home-notifier
$ npm install
$ node example.js
Endpoints:
    http://192.168.1.20:8091/google-home-notifier
    https://xxxxx.ngrok.io/google-home-notifier
GET example:
curl -X GET https://xxxxx.ngrok.io/google-home-notifier?text=Hello+Google+Home  - to play given text
curl -X GET https://xxxxx.ngrok.io/google-home-notifier?text=http%3A%2F%2Fdomain%2Ffile.mp3 - to play from given url
POST example:
curl -X POST -d "text=Hello Google Home" https://xxxxx.ngrok.io/google-home-notifier - to play given text
curl -X POST -d "http://domain/file.mp3" https://xxxxx.ngrok.io/google-home-notifier - to play from given url

```
#### Raspberry Pi
If you are running from Raspberry Pi make sure you have the following before nunning "npm install":
Use the latest nodejs dist.
```sh
curl -sL https://deb.nodesource.com/setup_7.x | sudo -E bash -
sudo apt-get install nodejs
```
Also install these packages:
```sh
sudo apt-get install git-core libnss-mdns libavahi-compat-libdnssd-dev
```

## After "npm install"

Modify the following file "node_modules/mdns/lib/browser.js"
```sh
vi node_modules/mdns/lib/browser.js
```
Find this line:
```javascript
Browser.defaultResolverSequence = [
  rst.DNSServiceResolve(), 'DNSServiceGetAddrInfo' in dns_sd ? rst.DNSServiceGetAddrInfo() : rst.getaddrinfo()
, rst.makeAddressesUnique()
];
```
And change to:
```javascript
Browser.defaultResolverSequence = [
  rst.DNSServiceResolve(), 'DNSServiceGetAddrInfo' in dns_sd ? rst.DNSServiceGetAddrInfo() : rst.getaddrinfo({families:[4]})
, rst.makeAddressesUnique()
];
```
