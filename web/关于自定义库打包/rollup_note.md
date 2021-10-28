## rollup的使用笔记


### 聊聊我在使用rollup打包
在某个项目中要对密码进行加密， 主要用到的是crypto-js和node-rsa的库来操作，
````
import  CryptoJS from 'crypto-js'
import  NodeRSA from 'node-rsa'
function rsaEncrypt(params) {
    const aes = CryptoJS.lib.WordArray.random(16).toString();
    const aes_key = aes.substr(0, 16);
    const aes_iv = aes.substr(16);
    const aes_key_utf = CryptoJS.enc.Utf8.parse(aes_key);
    const aes_iv_utf = CryptoJS.enc.Utf8.parse(aes_iv);
    const srcs = CryptoJS.enc.Utf8.parse(params);

    // rsa-key加密
    const rsa = new NodeRSA();

    rsa.setOptions({
        // environment: 'browser',
        encryptionScheme: 'pkcs1'
    });
    rsa.importKey({
        n: RSA_PUBLICK_KEY, // RSA_PUBLICK_KEY 是rsa公钥key
        e: 65537
    });


    const rsa_encrypt = rsa.encrypt(aes, 'base64');
    // aes加密
    const ciphertext = CryptoJS.AES.encrypt(srcs, aes_key_utf, { iv: aes_iv_utf, mode: CryptoJS.mode.CBC, padding: CryptoJS.pad.Pkcs7 });
    const aes_encrypt = ciphertext.toString();

    return {
        key: rsa_encrypt,
        pwd: aes_encrypt
    }
}


````
我需要把这个功能用rollup打包成umd格式的js库， 这样我可以直接引入js在H5项目(在浏览器中运行)中直接使用。

rollup的配置如下：
````
import json from '@rollup/plugin-json'
import nodePolyfills from 'rollup-plugin-node-polyfills';
const pkg = require('./package.json')


const version = process.env.VERSION || pkg.version
// 设置头部注释信息
const banner =
    '/*!\n' +
    ` * jfstock-h5-crypto v${version}\n` +
    ` * (c) ${new Date().getFullYear()} jf-stock\n` +
    ' * Released under the MIT License.\n' +
    ' */'

// 设置尾部注释信息
const footer = `/** ${new Date()} **/`
import { terser } from "rollup-plugin-terser";

export default {
    input: 'src/index.js',
    output: {
        file: 'dist/jfstock-crypto.js',
        name: 'JFH5CRYPTO',
        format: 'umd',
        globals: {
        },
        banner,
        footer,
    },
    plugins: [    
        json({
            namedExports: false
        }),
        require('@rollup/plugin-commonjs')({
            sourceMap: false
        }),
        nodePolyfills(),
        require('@rollup/plugin-node-resolve').nodeResolve({
            browser: true,
            preferBuiltins: false            
        }) ,
        terser()       
    ],
    treeshake: {
        moduleSideEffects: false
    }
}

````
对这个配置使用到的几个包做一下说明：
- @rollup/plugin-commonjs: 将CommonJs模块转为ES6, 这样我们可以直接使用import引入crypto-js和node-rsa中的功能， 如果不用这个， 则只能使用require去引入了， 用require引入的去打包的时候并不会进行babel或者polyfill, 我们直接在浏览器中是无法使用的
- rollup-plugin-node-polyfills   转义node相关模块的依赖， 但是这个模块无法转义node的自有库crypto和fs, 打包出来这两个库对应的都是空对象
- @rollup/plugin-node-resolve   使用节点解析算法定位模块的汇总插件。 这个包主要是因为在有些第三方库中用到了node的自有库， 在用rollup进行打包的时候要能打进去， 就必须用这个插件。 
- rollup-plugin-terser 对打包出来的js文件进行压缩

node-rsa中使用到crypto库（这个库是node的自有库），这样配置后是无法打进去的， 因为rollup-plugin-node-polyfills无法处理这个库， 就算加了crypto:true， 它也只是将crypto指向crypto-browserify, 打包出来仍然会有问题。

解决：
将node-rsa中的crypto的使用都改为crypto-browserify, 并且在该库中安装crypto-browserify， 再打包就可以了。


