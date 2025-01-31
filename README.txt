= eaal
* http://eaal.rubyforge.org

== DESCRIPTION:

EAAL (Eve API Access Layer) is a ruby library for accessing data of the API of 
the game Eve Online

== FEATURES/PROBLEMS:

* does not need to change when EVE API gets new pages
* has a build in file cache which respects eve caching times (usage= EAAL.cache = EAAL::Cache::FileCache.new('path/to/save/files/in'))

== SYNOPSIS:

Usage:
Initialize the API Object

  api = EAAL::API.new("key ID", "key verification code"[, "scope for requests"])

the scope is the one used for the API requests, 
ex. account/char/corp/eve/map/server
see http://wiki.eve-id.net/APIv2_Page_Index
the scope can be changed during runtime and defaults to account

Request Information
  result = api.ApiPage
this will return an Object of type ScopeApiPageResult which then
can be used to do read the api result

Example 1, getting a list of characters on the account:

  api = EAAL::API.new("key ID", "key verification code")
  result = api.Characters
  result.characters.each{|character|
      puts character.name
  }

Example 2, getting the id for a given character name
 api = EAAL::API.new("key ID", "key verification code")
 api.scope = "eve"
 result = api.CharacterID(:names => "Peter Powers")
 puts result.characters.first.characterID

Example 3, Example 2 in short
 puts EAAL::Api.new("key ID", "key verification code").CharacterID(:names => "Peter Powers").characters.name
 

Errors returned by the EVE API are handled a bit unique,
since i wanted to have them pretty much dynamic (so i dont need to hack EAAL 
whenever CCP adds a new Error) you have to use dynamic created classes to catch 'em
(if you dont want to catch EAAL::Exception::EveAPIException in general)
so what you do is:

Example 4, catching a specific EVE API Exception
  begin
    api.Killlog("characterID" => "12345") # this example of course 
                                          # assumes your not having the key for character 12345 loaded ;)
  rescue EAAL::Exception.EveAPIException(201)
    #dosomething
  end

EAAL comes with a simple file cache, which can be used by doing
  EAAL.cache = EAAL::Cache::FileCache.new(path)
if you dont give a path it defaults to $HOME/.eaal/cache

Example 5, doing a cached request
  require 'eaal'
  EAAL.cache = EAAL::Cache::FileCache.new               # set cache
  api = EAAL::API.new("key ID", "key verification code")        # initialize API object
  charid = api.Characters.characters.first.characterID  # get the characterID of the first character on account
  api.scope = "char"                                    # set scope to "char"
  kills = api.Killlog("characterID" => charid).kills    # request Killlog API Page

both requests in this example will check if the XML is allready available in the cache folder,
if it is they will check if cachedUntil expired, if it did requests will be made to the API, if it didnt
the XML file will be load.




== REQUIREMENTS:

* hpricot
* activesupport

== INSTALL:

* sudo gem install eaal

== THANKS:
special thanks go to James "Ix_Forres" Harrison for his code cleanups and 
his memcache cache handler
thanks also go to Davide Rambaldi and Ian Delahorno who contributed
several bug fixes.
== LICENSE:

(The MIT License)

Copyright (c) 2008 Peter Petermann

Permission is hereby granted, free of charge, to any person obtaining
a copy of this software and associated documentation files (the
'Software'), to deal in the Software without restriction, including
without limitation the rights to use, copy, modify, merge, publish,
distribute, sublicense, and/or sell copies of the Software, and to
permit persons to whom the Software is furnished to do so, subject to
the following conditions:

The above copyright notice and this permission notice shall be
included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED 'AS IS', WITHOUT WARRANTY OF ANY KIND,
EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT.
IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY
CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT,
TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE
SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
