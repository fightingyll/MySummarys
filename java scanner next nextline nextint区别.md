next表示返回第一个字符串
而nextLine（）方法的结束符只是Enter键，即nextLine（）方法返回的是Enter键之前的所有字符，它是可以得到带空格的字符串的。

简单的说nextLine() 返回的是一行。而next() 返回的只是第一个输入。 
比如；输入hello java 
nextLine() 读的是hello java 
next() 读的是hello 
next遇到第一个有效字符（非换行 分隔）开始扫描 到第一个间隔或空格结束 读取第一个字符串
nextline扫描到enter 读取一行
重要：next和nextline在一起协作时候 会各自调取使用的范围 而不是独立对获取的进行处理 例如next读取第一个字符串后 nextline会从第一个字符串结尾开始读取到行末 而不是整个第一行 