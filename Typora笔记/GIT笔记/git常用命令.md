#### 1.git忽略而不提交文件的三种情形

 1.从未提交过的文件

```
   添加路径或文件到.gitignore当中
```

2.已经推送（push）过的文件，想从git远程库中删除，并在以后的提交中忽略，但是却还想在本地保留这个文件 执行命令

```
git rm --cached app/config.js 
然后进行commit和push即可
```

 3.已经推送（push）过的文件，想在以后的提交时忽略此文件，即使本地已经修改过，而且不删除git远程库中相应文件

```
git update-index --assume-unchanged app/config.js   // 后面的app/config.js是要忽略的文件的路径，或者文件夹
git update-index --no-assume-unchanged app/config.js   // 恢复git跟踪
```

