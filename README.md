# Demo of govuk-frontend performance in Rails apps

Bare rails application with various branches to demonstrate performance of
[govuk-frontend](https://github.com/alphagov/govuk-frontend) within a Rails
app. This was put together off the back of various concerns with asset
performance on GOV.UK apps where digging into the problem revealed a lot of
the time was spent importing the files for govuk-frontend.

## With govuk-frontend using sassc

[master branch](https://github.com/kevindew/govuk-frontend-rails-performance/tree/master)

govuk-frontend node module installed via yarn using sassc.

As a web request

```
rails tmp:clear
rails s
time curl -s -o /dev/null http://localhost:3000/assets/application.css
=> curl -s -o /dev/null http://localhost:3000/assets/application.css  0.01s user 0.01s system 0% cpu 7.056 total
```

In console

```
rails tmp:clear && rails c
=> Running via Spring preloader in process 79910
=> Loading development environment (Rails 6.0.1)
irb(main):001:0> puts Benchmark.measure { Rails.application.assets["application.css"] }
=>  2.825796   2.334398   5.160194 (  7.494180)
irb(main):002:0> puts Benchmark.measure { Rails.application.assets["application.css"] }
=>  0.351671   0.440030   0.791701 (  1.667473)
```

Note: very long time on warm cache

Using sassc directly

```
time sassc app/assets/stylesheets/application.scss -I node_modules
=> sassc app/assets/stylesheets/application.scss -I node_modules  0.19s user 0.03s system 83% cpu 0.271 total
```

## With govuk-frontend with duplicate imports removed and sassc

[govuk-frontend-import-culled-sassc branch](https://github.com/kevindew/govuk-frontend-rails-performance/tree/govuk-frontend-import-culled-sassc)

govuk-frontend installed as git repo from https://github.com/kevindew/govuk-frontend-import-test
using sassc. This version of govuk-frontend has imports only called once.

As a web request

```
rails tmp:clear
rails s
time curl -s -o /dev/null http://localhost:3000/assets/application.css
=> curl -s -o /dev/null http://localhost:3000/assets/application.css  0.01s user 0.01s system 1% cpu 1.142 total
```

In console

```
rails tmp:clear && rails c
=> Running via Spring preloader in process 81852
=> Loading development environment (Rails 6.0.1)
irb(main):001:0> puts Benchmark.measure { Rails.application.assets["application.css"] }
=>  0.373088   0.124207   0.497295 (  0.552520)
irb(main):002:0> puts Benchmark.measure { Rails.application.assets["application.css"] }
=>  0.005388   0.001871   0.007259 (  0.007781)
```

Using sassc directly

```
time sassc app/assets/stylesheets/application.scss -I node_modules
=> sassc app/assets/stylesheets/application.scss -I node_modules  0.15s user 0.02s system 80% cpu 0.217 total
```

## With govuk-frontend and Ruby SASS

[govuk-frontend-npm branch](https://github.com/kevindew/govuk-frontend-rails-performance/tree/govuk-frontend-npm)

govuk-frontend node module installed via yarn using Ruby SASS


As a web request

```
rails tmp:clear
rails s
time curl -s -o /dev/null http://localhost:3000/assets/application.css
=> curl -s -o /dev/null http://localhost:3000/assets/application.css  0.01s user 0.01s system 0% cpu 15.523 total
```

In console

```
rails tmp:clear && rails c
=> Running via Spring preloader in process 56965
=> Loading development environment (Rails 6.0.1)
irb(main):001:0> puts Benchmark.measure { Rails.application.assets["application.css"] }
=>  9.824596   2.587184  12.411780 ( 15.556202)
irb(main):002:0> puts Benchmark.measure { Rails.application.assets["application.css"] }
=>  0.003977   0.001489   0.005466 (  0.007234)
```

## With govuk-frontend with duplicate imports removed and Ruby SASS

[govuk-frontend-import-culled branch](https://github.com/kevindew/govuk-frontend-rails-performance/tree/govuk-frontend-import-culled)

govuk-frontend installed as git repo from https://github.com/kevindew/govuk-frontend-import-test using Ruby SASS

As a web request

```
rails tmp:clear
rails s
time curl -s -o /dev/null http://localhost:3000/assets/application.css
=> curl -s -o /dev/null http://localhost:3000/assets/application.css  0.01s user 0.01s system 0% cpu 3.567 total
```

In console

```
rails tmp:clear && rails c
Running via Spring preloader in process 68512
Loading development environment (Rails 6.0.1)
irb(main):001:0> puts Benchmark.measure { Rails.application.assets["application.css"] }
=> 2.552180   0.241481   2.793661 (  2.913921)
irb(main):002:0> puts Benchmark.measure { Rails.application.assets["application.css"] }
=> 0.004052   0.001280   0.005332 (  0.005758)
```

## With govuk-frontend using webpacker

[govuk-frontend-npm-webpacker branch](https://github.com/kevindew/govuk-frontend-rails-performance/tree/govuk-frontend-npm-webpacker)

SCSS is compiled via webpacker. There was very little work put into the webpack configuration so use this info with caution.

```
bin/webpack
Hash: 39e378f79d42d54899dd
Version: webpack 4.41.2
Time: 5006ms
Built at: 12/03/2019 1:34:58 PM
                                     Asset       Size       Chunks                         Chunk Names
              css/application-1f4b8384.css    111 KiB  application  [emitted] [immutable]  application
          css/application-1f4b8384.css.map    179 KiB  application  [emitted] [dev]        application
    js/application-17bb4a000ce34a66d23a.js   3.96 KiB  application  [emitted] [immutable]  application
js/application-17bb4a000ce34a66d23a.js.map   3.63 KiB  application  [emitted] [dev]        application
                             manifest.json  640 bytes               [emitted]
Entrypoint application = css/application-1f4b8384.css js/application-17bb4a000ce34a66d23a.js css/application-1f4b8384.css.map js/application-17bb4a000ce34a66d23a.js.map
[./app/javascript/packs/application.scss] 39 bytes {application} [built]
    + 1 hidden module
Child mini-css-extract-plugin node_modules/css-loader/dist/cjs.js??ref--7-1!node_modules/postcss-loader/src/index.js??ref--7-2!node_modules/sass-loader/dist/cjs.js??ref--7-3!app/javascript/packs/application.scss:
    Entrypoint mini-css-extract-plugin = *
    [./node_modules/css-loader/dist/cjs.js?!./node_modules/postcss-loader/src/index.js?!./node_modules/sass-loader/dist/cjs.js?!./app/javascript/packs/application.scss] ./node_modules/css-loader/dist/cjs.js??ref--7-1!./node_modules/postcss-loader/src??ref--7-2!./node_modules/sass-loader/dist/cjs.js??ref--7-3!./app/javascript/packs/application.scss 340 KiB {mini-css-extract-plugin} [built]
        + 1 hidden module
```


## With govuk-frontend using webpacker with imports culled

[govuk-frontend-npm-webpacker-import-culled branch](https://github.com/kevindew/govuk-frontend-rails-performance/tree/govuk-frontend-npm-webpacker-import-culled)

govuk-frontend is compiled via webpacker, using the import culled branch. Again, no time put into webpacker configuration so use numbers with caution.

```
bin/webpack
Hash: 61cb8b8910ff5a7a9a38
Version: webpack 4.41.2
Time: 2477ms
Built at: 12/03/2019 1:37:38 PM
                                     Asset       Size       Chunks                         Chunk Names
              css/application-45bb3abc.css    110 KiB  application  [emitted] [immutable]  application
          css/application-45bb3abc.css.map    173 KiB  application  [emitted] [dev]        application
    js/application-17bb4a000ce34a66d23a.js   3.96 KiB  application  [emitted] [immutable]  application
js/application-17bb4a000ce34a66d23a.js.map   3.63 KiB  application  [emitted] [dev]        application
                             manifest.json  640 bytes               [emitted]
Entrypoint application = css/application-45bb3abc.css js/application-17bb4a000ce34a66d23a.js css/application-45bb3abc.css.map js/application-17bb4a000ce34a66d23a.js.map
[./app/javascript/packs/application.scss] 39 bytes {application} [built]
    + 1 hidden module
Child mini-css-extract-plugin node_modules/css-loader/dist/cjs.js??ref--7-1!node_modules/postcss-loader/src/index.js??ref--7-2!node_modules/sass-loader/dist/cjs.js??ref--7-3!app/javascript/packs/application.scss:
    Entrypoint mini-css-extract-plugin = *
    [./node_modules/css-loader/dist/cjs.js?!./node_modules/postcss-loader/src/index.js?!./node_modules/sass-loader/dist/cjs.js?!./app/javascript/packs/application.scss] ./node_modules/css-loader/dist/cjs.js??ref--7-1!./node_modules/postcss-loader/src??ref--7-2!./node_modules/sass-loader/dist/cjs.js??ref--7-3!./app/javascript/packs/application.scss 334 KiB {mini-css-extract-plugin} [built]
        + 1 hidden module
```

## Base case

[bare-app branch](https://github.com/kevindew/govuk-frontend-rails-performance/tree/bare-app)

No govuk-frontend installed, using sass-rails 5 which still has Ruby SASS. Default Rails installation

As a web request

```
rails tmp:clear
rails s
time curl -s -o /dev/null http://localhost:3000/application.css
curl -s -o /dev/null http://localhost:3000/application.css  0.01s user 0.01s system 1% cpu 0.907 total
```

In console

```
rails tmp:clear && rails c
Running via Spring preloader in process 49229
Loading development environment (Rails 6.0.1)
irb(main):001:0> puts Benchmark.measure { Rails.application.assets["application.css"] }
  0.175359   0.024084   0.199443 (  0.202119)
=> nil
```


