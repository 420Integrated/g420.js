g420.js
=======
Start and stop [g420](https://github.com/420integrated/go-420coin) from Node.js.

Usage
-----

```
$ npm install g420
```
To use g420.js, simply require it:
```javascript
var g420 = require("g420");
```

### Starting and stopping g420

g420's `start` method accepts a configuration object, which uses the same flags as the g420 command line client.  (Here, the flags are organized into an object.)  Flags that are not accompanied by a value on the command line (for example, `--mine`) should be passed in as `{ flag: null }`.
```javascript
var options = {
    networkid: "420",
    port: 13013,
    rpcport: 6174,
    mine: null
};

g420.start(options, function (err, proc) {
    if (err) return console.error(err);
    // get your g420 on!
});
```
The callback's parameter `proc` is the child process, which is also attached to the `g420` object (`g420.proc`) for your convenience.

When you and g420 have had enough lively times, `stop` kills the underlying g420 process:
```javascript
g420.stop(function () {
    // no more lively times :( 
});
```

### Listeners

The callback for `start` fires after g420 has successfully started.  Specifically, it looks for `"IPC service started"` in g420's stderr.  If you want to change the start callback trigger to something else, this can be done by replacing g420's default listeners.  `g420.start` accepts an optional second parameter which should specify the listeners to overwrite, for example:
```javascript
{
    stdout: function (data) {
        process.stdout.write("I got a message!! " + data.toString());
    },
    stderr: function (data) {
        if (data.toString().indexOf("Protocol Versions") > -1) {
            g420.trigger(null, g420.proc);
        }
    },
    close: function (code) {
        console.log("It's game over, man!  Game over!");
    }
}
```
In the above code, `g420.trigger` is the callback passed to `g420.start`.  (This callback is stored so that the startup trigger can be changed if needed.)  These three listeners (`stdout`, `stderr`, and `close`) are the only listeners which can be specified in this parameter, since only these three are set by default in `g420.start`.

If you want to swap out or add other listeners (after the initial startup), you can use the `g420.listen` convenience method:
```javascript
g420.listen("stdout", "data", function (data) { process.stdout.write(data); });
```
This example (re-)sets the "data" listener on stdout by setting `g420.proc.stdout._events.data = function (data) { process.stdout.write(data); }`.

Tests
-----

g420.js tests use fourtwentyrpc to send some basic requests to g420.
```
$ npm test
```
