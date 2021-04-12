title: Apache Commons CLI
author: Jiezhi.G
email: Jiezhi.G@gmail.com
premalink: fix_gravatar
thumbnailImage: 'http://qn-jiezhi.nospider.net/apachecli-logo.png-thumb2'
metaAlignment: center
coverImage: 'http://qn-jiezhi.nospider.net/10126038,2560,1600.jpg-cover'
coverCaption: Default
coverMeta: out
comments: true
date: 2015-10-13 15:18:20
url:
tags:
- Java
- Apache
- CLI
categories:
- Java
photos:
---
Apache Commons 中的CLI库提供了解析命令行选项的API。同样也可以帮你打印出标准的帮助信息。
<!--more-->

在*nix系统中使用命令行，经常会需要加参数进行不同的操作，Apache Commons提供的这个库可以帮你实现这样的功能。

#介绍

[项目官方地址](http://commons.apache.org/proper/commons-cli/index.html)
[下载地址](http://commons.apache.org/proper/commons-cli/download_cli.cgi)

#使用
直接代码
```Java
import org.apache.commons.cli.CommandLine;
import org.apache.commons.cli.CommandLineParser;
import org.apache.commons.cli.DefaultParser;
import org.apache.commons.cli.HelpFormatter;
import org.apache.commons.cli.Options;
import org.apache.commons.cli.ParseException;


public class CliTest {

    public static void main(String[] args) {
        // TODO Auto-generated method stub
        Options options = new Options();

        options.addOption("t", false, "display current time");
        options.addOption("c", true, "country code");

        HelpFormatter formatter = new HelpFormatter();
        formatter.printHelp("help", options);

        CommandLineParser parser = new DefaultParser();
        try {
            CommandLine cmd = parser.parse(options, args);
            // No argument command 't'
            if (cmd.hasOption("t")){
                System.out.println("Yes");
            } else {
                System.out.println("No");
            }

            // With argument command 'c'
            String countryCode = cmd.getOptionValue("c");
            if (countryCode == null) {
                System.out.println("no code");
            } else {
                System.out.println("code:" + countryCode);
            }
        } catch (ParseException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
            System.exit(-1);
        }

    }

}
```

加参数**-t -c cn**执行得到的结果:
```
usage: help
 -c <arg>   country code
 -t         display current time
Yes
code:cn

```
