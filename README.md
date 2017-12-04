# UAParser

[![Build Status](https://travis-ci.org/JuliaWeb/UAParser.jl.svg?branch=master)](https://travis-ci.org/JuliaWeb/UAParser.jl)
[![Coverage Status](https://coveralls.io/repos/JuliaWeb/UAParser.jl/badge.svg)](https://coveralls.io/r/JuliaWeb/UAParser.jl)

[![UAParser](http://pkg.julialang.org/badges/UAParser_0.3.svg)](http://pkg.julialang.org/?pkg=UAParser&ver=0.3)
[![UAParser](http://pkg.julialang.org/badges/UAParser_0.4.svg)](http://pkg.julialang.org/?pkg=UAParser&ver=0.4)

UAParser is a Julia port of [ua-parser](https://github.com/tobie/ua-parser), which itself is a multi-language port of [BrowserScope's](http://www.browserscope.org) [user agent string parser](http://code.google.com/p/ua-parser/). Per the [README file](https://github.com/tobie/ua-parser/blob/master/README.markdown) from that project:

> "The crux of the original parser--the data collected by [Steve Souders](http://stevesouders.com/) over the years--has been extracted into a separate [YAML file](https://github.com/tobie/ua-parser/blob/master/regexes.yaml) so as to be reusable _as is_ by implementations in other programming languages."

UAParser is a limited Julia implementation heavily influenced by the [Python code](https://github.com/tobie/ua-parser/tree/master/py) from the ua-parser
library.

New regexes have were retrieved from [here](https://github.com/ua-parser/uap-core/blob/master/regexes.yaml). This was the most up-to-date source that could be
found as of 2017-10-18.

## UAParser API

The API for UAParser revolves around three functions: `parsedevice`, `parseos` and `parseuseragent`. Each function takes one argument, `user_agent_string::AbstractString` and returns a custom Julia type: `DeviceResult`, `OSResult`, or `UAResult`. The structure of each type is as follows:

```
  DeviceResult: family, brand, model

  UAResult: family, major, minor, patch

  OSResult: family, major, minor, patch, patch_minor
```

## Code examples

```julia
  using UAParser

  #Example user-agent string
  user_agent_string = "Mozilla/5.0 (iPhone; CPU iPhone OS 5_1 like Mac OS X) AppleWebKit/534.46 (KHTML, like Gecko) Version/5.1 Mobile/9B179 Safari/7534.48.3"

  #Get device from user-agent string
  parsedevice(user_agent_string) #> DeviceResult("iPhone", "Apple", "iPhone")

  #Get browser information from user-agent string
  parseuseragent(user_agent_string) #> UAResult("Mobile Safari","5","1",nothing)

  #Get os information
  parseos(user_agent_string) #> OSResult("iOS","5","1",nothing,nothing)

```

You can index into the results of these functions like any other Julia composite type.

```julia
  #Get just browser information, no version information
  x1 = parseuseragent(user_agent_string)
  x1.family #> "Mobile Safari"

  #Get the os, no version information
  x2 = parseos(user_agent_string)
  x2.family #> "iOS"
```

Each function is vectorized using `Base.@vectorize_1arg`, so passing an array of user-agent strings will return an array. Finally, DataFrame methods have been defined for arrays of each UAParser type, so calling `DataFrame(DeviceResult)` will transform the array to a DataFrame.

## Licensing

The licensing of the UAParser Julia module is under the [default MIT Expat license](https://github.com/JuliaWeb/UAParser.jl/blob/master/LICENSE.md). The data
contained in regexes.yaml is Copyright 2009 Google Inc. and available under the Apache License, Version 2.0.
