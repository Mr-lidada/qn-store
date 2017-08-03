最近对自己新开通的博客添加了七牛云存储的功能，网站上所有的图片等资源全部存放在七牛云。
感谢[@Minwe LUO](https://github.com/minwe/qn-store)发表在github上的教程。

### 说说自己的部署过程
GHOST 1.3.0的目录结构和之前版本不是特别一样，我的博客是用GHOST-CLI部署的，所以大致的文件结构是这个样子：

├── config.production.json 

├── content

├── current

├── system

└── versions


网站运行则依赖current文件夹下的内容，他下面的目录结构是这样的：

├── content

├── core

├── Gruntfile.js

├── index.js

├── LICENSE

├── MigratorConfig.js

├── node_modules

├── package.json

├── PRIVACY.md

├── README.md

└── yarn.lock

你会发现和以前的目录结构不太一样：

├─buster

├─content

├─core

├─node_modules

├─config.example.js

├─config.js

├─Gruntfile.js

├─index.js

├─LICENSE

├─npm-shrinkwrap.json

├─package.json

├─PRIVACY.md

└─README.md


config.js不见了，实际上config.js变成了/current/core/server/config下的defaults.json文件。所以配置"storage": {  ...}的话就要在defaults.json里面配置了。
首先吧qn-store的插件放在/current/core/server/adapters/storage目录下然后在qn-store下执行npm install就可以了。
```
git clone https://github.com/ImCodeMonkey/qn-store.git
cd qn-store
npm install
```
最后在defaults.json加入下面的配置就可以了。
```
"storage": {
    "active": "qn-store",
    "qn-store": {
      "accessKey": "xxxxxxxxx",
      "secretKey": "xxxxxxxxx",
      "bucket": "xxxxx",
      "origin": "http://xxx.clouddn.com",
      "uploadURL": "http://up-z2.qiniu.com",    //我是华南区，所以是这个地址了
      "fileKey": {
        "safeString": true,
        "prefix": "YYYYMM/"
      }
    }
},
```
### 更多设置
```
storage: {
  active: 'qn-store',
  'qn-store': {
    accessKey: 'your access key',
    secretKey: 'your secret key',
    bucket: 'your bucket name',
    origin: 'http://xx.xx.xx.glb.clouddn.com',
    // timeout: 3600000, // default rpc timeout: one hour, optional
    // if your app outside of China, please set `uploadURL` to `http://up.qiniug.com/`
    // uploadURL: 'http://up.qiniu.com/'

    // file storage key config [optional]
    // if `fileKey` not set, Qiniu will use `SHA1` of file content as key.
    fileKey: {
      // use Qiniu hash as file basename, if set, `safeString` will be ignored
      hashAsBasename: false,
      safeString: true, // use Ghost safaString util to rename filename, e.g. Chinese to Pinyin
      prefix: 'YYYY/MM/', // {String} will be formated by moment.js, using `[]` to escape,
      suffix: '', // {String} string added before file extname.
      extname: true // keep file's extname
    }
    // take `外面的世界 x.jpg` as example,
    // applied above options, you will get an URL like below after uploaded:
    //  http://xx.xx.xx.glb.clouddn.com/2016/06/wai-mian-de-shi-jie-x.jpg
  }
}
```
