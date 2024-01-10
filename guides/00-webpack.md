![webpack.png](assets/webpack-logo.png)

> **_NOTE_**: Webpack has default values and doesn't need a configuration file, but exploiting its possibility can be very useful.

# Configuring Webpack on a clean start

## Make a minimal stable project shell

1. Create a package.json with an empty object in it.

2. Install dependencies with pnpm.

   - Dependencies: express, react, react-dom, wouter
   - Dev Dependencies: css-loader, html-webpack-plugin, prettier, style-loader,swc-loader, tsconfig-paths-webpack-plugin, tsconfig-to-swcconfig, tsx, typescript, webpack, webpack-cli, webpack-dev-server, @swc/core, @swc/register, @types/express, @types/node, @types/react, @types/react-dom

3. Create webpack.config.ts

   - Create configuration object:

   ```
   export default function config(_: any, { mode }): webpack.Configuration {
      return {
         mode,
         resolve: {},
         module: {},
         plugins: [],
         entry: {},
         output: {},
         devtool: '',
         devServer: {}
            }
   }
   ```

   - Fill in the basic settings:

     - Imports as follows:

     ```
     import { resolve } from 'node:path';

     import webpack from 'webpack';
     import 'webpack-dev-server';
     import TsconfigPathsPlugin from 'tsconfig-paths-webpack-plugin';
     import { convert } from 'tsconfig-to-swcconfig';
     ```

     - resolve, to determine how the modules are resolved.

     ```
     resolve: {
              extensions: ['.tsx', '.js'],
              plugins: [new TsconfigPathsPlugin()],
           },

     ```

     - module.rules determines how modules are created.

     ```
     module: {
           rules: [
              {
                 test: /.[jt]sx?$/,
                 exclude: /node_modules/,
                 use: {
                    loader: 'swc-loader',
                    options: convert(undefined, process.cwd(), {
                       jsc: { baseUrl: process.cwd() },
                    }),
                 },
              },
           ],
      },

     ```

     - plugins to customize the webpack build process.

     ```
     plugins: [
     	new webpack.DefinePlugin({
     		'process.env.NODE_ENV': JSON.stringify(mode),
     	}),
     ],

     ```

     - entry points to the file where the application bundling process starts.

     ```
     entry: {
     	client: 'src/entry.tsx',
     },
     ```

     - output determines where the bundled code is stored, folder and file.

     ```
     output: {
     	path: resolve('dist/client'),
     	filename: '[name].js',
     },

     ```

     - devtool and devServer provide additional tools and configuration for how the resulting code is bundled.

     ```
     devtool: 'inline-source-map',

     devServer: {
     	historyApiFallback: true,
     	hot: true,
     },
     ```

4. Create tsconfig.json, standard settings, only make sure to not use the current year's ES.

```
{
	"compilerOptions": {
		"target": "ES2022",
		"lib": ["ES2022", "DOM"],

		"module": "ESNext",
		"moduleResolution": "Node10",
		"esModuleInterop": true,

		"jsx": "react-jsx",

		"sourceMap": true,

		"paths": {
			"~/*": ["./src/*"]
		}
	}
}
```

5. Make sure to user prettier no matter what. Create a .prettierrc file in the root and use standard settings. Also make sure to enable format-on-save.

```
{
	"jsxSingleQuote": true,
	"singleQuote": true,
	"trailingComma": "all",
	"useTabs": true
}
```

6. Create a src folder
7. Create entry.tsx and app.tsx inside the src folder.

   - Create a functional component inside the app.tsx

   ```
   import { type FC } from 'react';

   export const App: FC = () => {
   return (
   <></>
   );
   };
   ```

   - Import the render function createRoot from React DOM in entry.tsx and import the App component to render it.

   ```
   import { createRoot } from 'react-dom/client';

   import { App } from '~/app';

   createRoot(document.getElementById('root')!).render(<App />);
   ```

8. Add css to the project.

   - webpack.config.ts > module.rules

   ```
   {
      test: /\.css$/i,
      use: ['style-loader', 'css-loader'],
   },

   ```

   - Create a css stylesheet file in the src. Could be called globals.css, for example.
   - Import it into the entry file.

   ```
   import './globals.css';
   ```

9. Add the HTML Webpack Plugin to customize the html template.

   - import the module into webpack.config.ts

   ```
   import HtmlWebpackPlugin from 'html-webpack-plugin';
   ```

   - webpack.config.ts > plugins

   ```
   new HtmlWebpackPlugin({
               title: 'Custom Stack',
               filename: 'index.html',
               template: 'index-template.html',
               favicon: 'public/favicon.ico',
            }),

   ```

   - Create index-template.html in the root directory. Make sure that the body has id='root'. Favicon is optional.

10. Create scripts in the package.json for running development mode and build.

```
"scripts": {
		"build-client": "webpack build --mode production",
		"dev": "webpack serve --mode development"
	},
```
