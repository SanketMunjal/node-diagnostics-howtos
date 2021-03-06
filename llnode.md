# Notes on llnode and lldb installation

## What's all this?

### lldb

It's a tool for analyzing low level diagnostics information from processes. Node.js in our case :)

### llnode

It's a plugin to lldb that shows real names of references/functions in JavaScript in lldb (normally you'd only get names for C++ functions and objects)

It's great that lldb+llnode can be used to read diagnostics from node, it used to be that only `mdb` on solaris based operating system could do that.

## Notes on installation

First install lldb using apt-get, then install the llnode plugin from npm:

[https://npmjs.org/package/llnode](https://www.npmjs.org/package/llnode)

To install the plugin use `npm install llnode` or follow the build instructions in the [github project](https://github.com/nodejs/llnode).

Note that lldb supports the use of a system plugin directory (`/usr/lib/lldb/plugins` on Linux). By installing a plugin in the system plugin directory you avoid having to run the lldb `plugin load llnode.so` command in each lldb session. The npm install does not add the llnode plugin to the system plugin directory. You can either copy the plugin: `sudo cp llnode.so /usr/lib/lldb/plugins` or if you are building from the github repository, install it using the `sudo make install-linux` build step.

*A global npm install of llnode, i.e. `npm install -g llnode`, does not work right now. Maybe we should raise an issue on llnode to have the -g install do the copy to the system plugin directory. It would need to run as sudo/root.*

*It should work on all flavors of ubuntu and most other distros with any version of lldb starting at 3.8. If you find out more, feel free to PR here*

*On 2017-02-03 I managed to install lldb-3.8 with latest llnode in xubuntu 16.04.1 kernel 4.4.0-62-generic x86_64*

## llnode commands

Once you install llnode, these commands become available in lldb.


---
`v8 help` Display the help  

`v8 nodeinfo` Display Node.js information summary

---
Display the JavaScript and C++ stack

`v8 bt [<number of frames>]`


  - output includes source file, line number, object addresses
  - display source code using `frame select  <frame number>` then `v8 source list`

---
List objects in the JavaScript heap

`v8 findjsobjects`

  - provides number of objects, total size and type name

---
Display JavaScript object/constructor/function

`v8 print <object address>`

`v8 inspect <object address>`

`v8 inspect <buffer object address>` Display raw address of buffer memory

`v8 inspect [-m -s -F] <object address>`

  - `-m` prints the object's map address
  - `-s` prints source code for function objects
    - see `v8 bt` above for source code of stackframe
  - `-F` prints full string values without truncation


---
Find JavaScript objects by type name

`v8 findjsinstances [-m -s -F] <type name>`

  - same output options as the `v8 inspect`

---
Find JavaScript objects by reference  

`v8 findrefs [-v -n -s]`

  - `-v <object address>` (default) finds objects that reference a specified object
  - `-n <name>` finds objects that reference a specified property name
  - `-s <string>` finds objects that reference a specified string
