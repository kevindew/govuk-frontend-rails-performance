# Demo of govuk-frontend performance in Rails apps

Bare rails application with various branches to demonstrate performance of
[govuk-frontend](https://github.com/alphagov/govuk-frontend) within a Rails
app. This was put together off the back of various concerns with asset
performance on GOV.UK apps where digging into the problem revealed a lot of
the time was spent importing the files for govuk-frontend.

## With govuk-frotend using sassc

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

## With govuk-frontend with imports slimmed and sassc

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

## With govuk-frotend and Ruby SASS

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

## With govuk-frontend with imports slimmed and Ruby SASS

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


