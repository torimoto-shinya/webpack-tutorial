# Webpack4 Tutorial

## URL
* https://www.valentinog.com/blog/webpack/

## 検証環境
```
$ node -v
v12.4.0

$ npm -v
6.9.0
```

## 手順
### webpackインストール
```
$ npm init -y
$ npm i webpack --save-dev
$ npm i webpack-cli --save-dev
```

### package.jsonを書き換え
```
"scripts": {
  "build": "webpack"
}
```

### ビルドを試してみる
```
$ npm run build
ERROR in Entry module not found: Error: Can't resolve './src' in '/home/my-app'
# → Webpack4では、./srcがデフォルトのエントリポイントとなる
```
```
$ mkdir src
$ touch src/index.js
```

### src/index.jsを編集
```
console.log(`I'm a silly entry point`);
```

### build
```
$ npm run build
WARNING in configuration
The 'mode' option has not been set, webpack will fallback to 'production' for this value. Set 'mode' option to 'development' or 'production' to enable defaults for each environment.
You can also set it to 'none' to disable any default behavior. Learn more: https://webpack.js.org/configuration/mode/
```

### WARNINGが出ないように、package.jsonを編集
```
"scripts": {
  "dev": "webpack --mode development",
  "build": "webpack --mode production"
}
```

### babelを使う
* webpackはES6の変換方法を知らないが、知っている人のloaderを持てる → babel-loader
```
$ npm i @babel/core babel-loader @babel/preset-env --save-dev
$ touch .babelrc
```

### .babelrcを編集
```
{
    "presets": [
        "@babel/preset-env"
    ]
}
```

### webpack4からは設定ファイルいらんかったのとちゃうの？
The concept of zero configuration in webpack 4 applies to:

* the entry point. Default to ./src/index.js
* the output. Default to ./dist/main.js
* production and development mode (no need to create 2 separate confs for production and development)

### babel使うのであれば、結局必要です webpack.config.js
```
$ touch webpack.config.js
```

### webpack.config.jsを編集
```
module.exports = {
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: "babel-loader"
        }
      }
    ]
  }
};
```

### build
```
$ npm run build
```

### webpack 4: the HTML webpack plugin
```
$ npm i html-webpack-plugin html-loader --save-dev
```

### webpack.config.jsの編集
```
const HtmlWebPackPlugin = require("html-webpack-plugin");
module.exports = {
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: "babel-loader"
        }
      },
      {
        test: /\.html$/,
        use: [
          {
            loader: "html-loader",
            options: { minimize: true }
          }
        ]
      }
    ]
  },
  plugins: [
    new HtmlWebPackPlugin({
      template: "./src/index.html",
      filename: "./index.html"
    })
  ]
};
```

### index.htmlを作る
```
$ touch src/index.html
```

### src/index.htmlの編集
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="utf-8">
    <title>webpack 4 quickstart</title>
</head>
<body>
    <div id="app">
    </div>
</body>
</html>
```

### CSSもいじくれるようになろう
```
$ npm i mini-css-extract-plugin css-loader --save-dev
```

### webpack.config.jsを編集
```
const HtmlWebPackPlugin = require("html-webpack-plugin");
const MiniCssExtractPlugin = require("mini-css-extract-plugin");
module.exports = {
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: "babel-loader"
        }
      },
      {
        test: /\.html$/,
        use: [
          {
            loader: "html-loader",
            options: { minimize: true }
          }
        ]
      },
      {
        test: /\.css$/,
        use: [MiniCssExtractPlugin.loader, "css-loader"]
      }
    ]
  },
  plugins: [
    new HtmlWebPackPlugin({
      template: "./src/index.html",
      filename: "./index.html"
    }),
    new MiniCssExtractPlugin({
      filename: "[name].css",
      chunkFilename: "[id].css"
    })
  ]
};
```

### CSSを作成
```
$ touch src/main.css
```

### src/main.cssの編集
```
body {
    line-height: 2;
}
```

### src/index.jsの編集
```
// 1行目に追加
import style from "./main.css";
```

### build
```
$ npm run build
```

### webpack-dev-server
```
$ npm i webpack-dev-server --save-dev
```

### package.jsonの編集
```
  "scripts": {
    "start": "webpack-dev-server --mode development --open",
    "build": "webpack --mode production"
  },

```

# webpack.config.jsの編集
```
const HtmlWebPackPlugin = require("html-webpack-plugin");
const MiniCssExtractPlugin = require("mini-css-extract-plugin");
module.exports = {
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: "babel-loader"
        }
      },
      {
        test: /\.html$/,
        use: [
          {
            loader: "html-loader",
            options: { minimize: false }
          }
        ]
      },
      {
        test: /\.css$/,
        use: [MiniCssExtractPlugin.loader, "css-loader"]
      }
    ]
  },
  plugins: [
    new HtmlWebPackPlugin({
      template: "./src/index.html",
      filename: "./index.html"
    }),
    new MiniCssExtractPlugin({
      filename: "[name].css",
      chunkFilename: "[id].css"
    })
  ],
  devServer: {
    host: '0.0.0.0',
    disableHostCheck: true
  }
};

```

### typescriptを書けるようにする
```
$ npm i typescript ts-loader --save-dev
$ touch tsconfig.json
```

### tsconfig.jsonの編集
```
{
  "compilerOptions": {
    "sourceMap": true,
    "target": "es5", // TSはECMAScript 5に変換
    "module": "es2015" // TSのモジュールはES Modulesとして出力
  }
}
```

### webpack.config.js
```
const HtmlWebPackPlugin = require("html-webpack-plugin");
const MiniCssExtractPlugin = require("mini-css-extract-plugin");
module.exports = {
  module: {
    entry: "./src/main.ts",
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: "babel-loader"
        }
      },
      {
        test: /\.html$/,
        use: [
          {
            loader: "html-loader",
            options: { minimize: false }
          }
        ]
      },
      {
        test: /\.css$/,
        use: [MiniCssExtractPlugin.loader, "css-loader"]
      },
      {
        // 拡張子 .ts の場合
        test: /\.ts$/,
        // TypeScript をコンパイルする
        use: "ts-loader"
      }
    ]
  },
  resolve: {
    extensions: ['.tsx', '.ts', '.js'],
  },
  plugins: [
    new HtmlWebPackPlugin({
      template: "./src/index.html",
      filename: "./index.html"
    }),
    new MiniCssExtractPlugin({
      filename: "[name].css",
      chunkFilename: "[id].css"
    })
  ],
  devServer: {
    host: '0.0.0.0',
    disableHostCheck: true
  }
};
```

### typescriptを書いてみる
```
$ touch src/main.ts

# ↑ TODO: main.tsの中身は適当に編集しておく
```

### main.tsをエントリーポイントにするため、webpack.config.jsの編集
```
const HtmlWebPackPlugin = require("html-webpack-plugin");
const MiniCssExtractPlugin = require("mini-css-extract-plugin");
module.exports = {
  entry: "./src/main.ts",
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: "babel-loader"
        }
      },
      {
        test: /\.html$/,
        use: [
          {
            loader: "html-loader",
            options: { minimize: false }
          }
        ]
      },
      {
        test: /\.css$/,
        use: [MiniCssExtractPlugin.loader, "css-loader"]
      },
      {
        // 拡張子 .ts の場合
        test: /\.ts$/,
        // TypeScript をコンパイルする
        use: "ts-loader"
      }
    ]
  },
  resolve: {
    extensions: ['.tsx', '.ts', '.js'],
  },
  plugins: [
    new HtmlWebPackPlugin({
      template: "./src/index.html",
      filename: "./index.html"
    }),
    new MiniCssExtractPlugin({
      filename: "[name].css",
      chunkFilename: "[id].css"
    })
  ],
  devServer: {
    host: '0.0.0.0',
    disableHostCheck: true
  }
};
```

### 動作確認
```
$ npm run start
```