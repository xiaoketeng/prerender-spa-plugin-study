# prerender-spa-plugin-study
记录学习预渲染的笔记
一、环境：vue: 3.10.0
二、插件：cnpm install prerender-spa-plugin --save-dev
三、在和package.json同级建立一个vue.config.js文件
vue.config.js加入内容：


const PrerenderSPAPlugin = require('prerender-spa-plugin');
const Renderer = PrerenderSPAPlugin.PuppeteerRenderer;
const path = require('path');


module.exports = {
configureWebpack: () => {
  if (process.env.NODE_ENV !== 'production') return;
     return {
       plugins: [
         new PrerenderSPAPlugin({
            // 生成文件的路径，也可以与webpakc打包的一致。
            // 下面这句话非常重要！！！
            // 这个目录只能有一级，如果目录层次大于一级，在生成的时候不会有任何错误提示，在预渲染的时候只会卡着不动。
            staticDir: path.join(__dirname, 'dist'),
            // 对应自己的路由文件，比如a有参数，就需要写成 /a/param1。
            routes: ['/', '/my'],
            // 这个很重要，如果没有配置这段，也不会进行预编译
            renderer: new Renderer({
              inject: {
               foo: 'bar'
              },
              headless: false,
              // 在 main.js 中 document.dispatchEvent(new Event('render-event'))，两者的事件名称要对应上。
              renderAfterDocumentEvent: 'render-event'
          })
        })
      ]
   };
 }
}

四、在main.js里的new Vue（{}）里加入这段代码

mounted() {
    document.dispatchEvent(new Event('render-event'));
}

五、打包项目 npm run build
六、查看是否做预渲染成功
    将打包的dis文件放到服务器里，进行访问，右击---查看网页源代码---如果里面有填充了当页给显示的内容，就说明预渲染成功。
