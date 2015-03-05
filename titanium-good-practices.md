titanium-good-practices
=======================

Make titanium easily and powerfully

### [Officer Best Practies](http://docs.appcelerator.com/titanium/latest/#!/guide/Coding_Best_Practices-section-30082362_CodingBestPractices-Don%27tExtendTitaniumPrototypes)

## On the whole
### Using CLI insteads of titanium studio to create, build, deploy, release or write
Imroving productivity, giving a automated solution
[Titanium Command-Line Interface Reference](http://docs.appcelerator.com/titanium/3.0/#!/guide/Titanium_Command-Line_Interface_Reference)

### You must use [Alloy](http://docs.appcelerator.com/titanium/3.0/#!/guide/Alloy_Quick_Start) to create MVC

#### Make sure the backbone document`s version is 0.9.2 when you read it
>Currently, Alloy is using Backbone version 0.9.2, which is not the most current version of Backbone. The documentation links in the Model guides link to version 0.9.2 of the Backbone documentation. If you go to the official Backbone site, the documentation might be out-of-sync with these guides.

### Create your CLI command like [jake](https://github.com/jakejs/jake)
jakefile (coffeescript)
```
appplatform = switch process.env.appplatform
  when 'ios', 'android' then process.env.appplatform
  else 'ios'

apptarget = switch process.env.apptarget
  when 'device' then process.env.apptarget
  else
    if appplatform is 'ios' then 'simulator' else 'emulator'

appdeploytype = switch process.env.appdeploytype
  when 'development', 'test' then process.env.appdeploytype
  else 'development'

appsimversion = switch process.env.appsimversion
  when '6.1' then "-S #{process.env.appsimversion} --retina"
  else '--retina --tall'

desc 'build app in normal way'
task 'build', ['prepare'], {async: true}, ->
  if appplatform is 'android' and apptarget isnt 'device'
    appappname = '' if appappname is 'rjhy'
    cmds = "ti build -d YourProductName -p #{appplatform} -D #{appdeploytype} -C 'Google Nexus 4 - 4.3 - API 18 - 768x1280'"
    jake.exec cmds, {interactive: true}, complete
  else
    jake.exec "ti build -d YourProductName -p #{appplatform} -T #{apptarget} -D #{appdeploytype} #{appsimversion}", {interactive: true}, complete
```

## Improve development efficiency
### Using [jslint](https://github.com/reid/node-jslint) to check code by CLI if you wirte javascript 

### Easily write xml by using [jade](https://github.com/jadejs/jade)
Modify alloy.jmk to redirect xml to jade
```
task('pre:compile', function(event, logger) {
    var wrench = require('wrench'),
      fs = require('fs'),
    jade = require('jade'),
    path = require('path'),
    appDir = path.join(event.dir.project, 'app');

  wrench.readdirSyncRecursive(appDir).forEach(function(view) {
    if (view.match(/\.jade$/) && view.indexOf('templates') === -1 && view.indexOf('includes') === -1) {
      try {
        fs.writeFileSync(
          path.join(appDir, view.replace(/\.jade$/, '.xml')),
          jade.compile(fs.readFileSync(path.join(appDir, view)).toString(), {
            filename: path.join(appDir, view),
            pretty: true
          })(event));
      } catch (e) {
        logger.error('ERROR: ' + view + '\n' + JSON.stringify(e));
        process.exit(1);
      }
    }
  });
});
```

### Using [tishadow](https://github.com/dbankier/TiShadow) to quickly build and update project
TiShadow provides Titanium developers the ability to deploy apps, run tests or execute code snippets live across all running iOS and Android devices.

Be careful about the assets of webview in tishadow

### Using [Genymotion](http://www.genymotion.com/) quicken building simulator on android 
[Installing Genymotion](http://docs.appcelerator.com/titanium/3.0/#!/guide/Installing_Genymotion)


## Check and avoid the error
### Make sure you exactly know the result of API on different platforms
example (coffeescript)
```
view = Ti.UI.createView
  height: 100%
  width: 100%

view.applyProperties
  backgroundColor: 'red'
  id: 'myview'
  cls: 'header'
 
console.log "My view`s cls is %s", view.cls

```
#### There are many mistake on another platform, so check it first

### If you could not ensure which mistake in your file then check source javascript file like 'Resources/$platform/alloy/controllers/index.js'

### Be careful use of Alloy.global and global value

### Create unit test in a light project, test your network in web first 

### At lastest, you could read [titanium-mobile source code](https://github.com/appcelerator/titanium_mobile) if you could not solve your error

### Don`t modify tainium-mobile source code and build your own titainum SDK to solve your problem although you can build it easily
[Building the Titanium SDK](http://docs.appcelerator.com/titanium/3.0/#!/guide/Building_the_Titanium_SDK_From_Source)


## Code specification

### The use of ‘Ti’ has been using 'Ti'
```
# file a
Ti.Network.getOnline()
```

```
# file b
# It`s wrong
Titanium.App.version
```

### Don`t use '$.' to export public things in controller
First and foremost all of the things in '$' will be extend to exports
Check the resource code, you will see 
```
_.extend($, exports);
```
Other man can easily read which thing you hope to make it public
```
# wrong
$.updateUserInfo = (userInfo) ->
```

```
# right
exports.updateUserInfo = (userInfo) ->
```

### Obviously using the platform
Don`t use 'else', although your application only support iOS and android
```
# wrong
if OS_IOS
  height = 45
else
  height = 40
```
```
# better
height = 45
if OS_ANDROID
  height = 40
```
```
# right
if OS_IOS
  height = 45
else if OS_ANDROID
  height = 40
```


## Titanium Module
### Check all of the titnaium API before you create it

### Read [document](http://docs.appcelerator.com/titanium/3.0/#!/guide/Using_a_Module) clearly before you create module
[iOS Module Development Guide](http://docs.appcelerator.com/titanium/3.0/#!/guide/iOS_Module_Development_Guide)
[android Module Development Guide](http://docs.appcelerator.com/titanium/3.0/#!/guide/Android_Module_Development_Guide)

### Search in module-market [http://gitt.io/] before you create module

### Read [source code](https://github.com/appcelerator/titanium_modules) before your create module
You should imitate the sytle of source code

## Third party library of javascript
### sync: [restapi](https://github.com/viezel/napp.alloy.adapter.restapi) [restsql](https://github.com/viezel/napp.alloy.adapter.restsql)

### date: [moment](https://github.com/moment/moment)

### async: [async](https://github.com/caolan/async)

### test: [chai](https://github.com/chaijs/chai) [mocha](https://github.com/mochajs/mocha)

### util: [underscore](http://underscorejs.org/)
Underscore is inner in titanium enviorment if the file is a controller
When you use underscore in lib/*.js, you should require it by yourself
Make sure the underscore document`s version equals titanium

## Finally
### Share your exprenrice to [Q&A](http://developer.appcelerator.com/questions/newest)

### Report issuses to [titanium](https://jira.appcelerator.org/)

### Wirte your good practices in this README.md

## Support, not good pracites
### Easily write code by using [CoffeeScript](http://coffeescript.org/), and check code by [coffeelint](http://www.coffeelint.org/)

### Best to [bind data](http://docs.appcelerator.com/titanium/3.0/#!/guide/Alloy_Data_Binding) in xml(jade)
```
<Alloy>
    <Collection src="book"/>
    <Window backgroundColor="white" onClose="cleanup">
        <ScrollableView dataCollection="book">
            <View layout="vertical">
                <ImageView image="{cover}" />
                <Label text="{title} by {artist}" />
            </View>
        </ScrollableView>	
    </Window>
</Alloy>
```
