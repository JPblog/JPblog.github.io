---
layout: post
title:  "Word Count——WC 项目"
date:   2020-03-25
excerpt: "软件工程个人项目"
project: true
tag:
- 软件工程 
- 终端
- C++
comments: false
---

> ### 个人项目WordCount(C++/QT)

GitHub项目地址：https://github.com/Nova-cjp/Word-Count

百度云链接：https://pan.baidu.com/s/1MSOXu0KbuUMJfJ8p8bwMwQ 提取码：jef2

## 一、PSP表格

| PSP2.1                                  | Personal Software Process Stages        | 预估耗时（分钟） | 实际耗时（分钟） |
| --------------------------------------- | --------------------------------------- | ---------------- | ---------------- |
| Planning                                | 计划                                    | 10               | 15               |
| · Estimate                              | · 估计这个任务需要多少时间              | 10               | 15               |
| Development                             | 开发                                    | 380              | 1240             |
| · Analysis                              | · 需求分析 (包括学习新技术)             | 90               | 150              |
| · Design Spec                           | · 生成设计文档                          | ----             | 10               |
| · Design Review                         | · 设计复审 (和同事审核设计文档)         | 5                | 5                |
| · Coding Standard                       | · 代码规范 (为目前的开发制定合适的规范) | 10               | 15               |
| · Design                                | · 具体设计                              | 30               | 60               |
| · Coding                                | · 具体编码                              | 150              | 240              |
| · Code Review                           | · 代码复审                              | 30               | 10               |
| · Test                                  | · 测试（自我测试，修改代码，提交修改）  | 60               | 270              |
| Reporting                               | 报告                                    | 100              | 40               |
| · Test Report                           | · 测试报告                              | 60               | 15               |
| · Size Measurement                      | · 计算工作量                            | 10               | 10               |
| · Postmortem & Process Improvement Plan | · 事后总结, 并提出过程改进计划          | 30               | 15               |
| 合计                                    |                                         | 515              | 1295             |

## 二、解题思路

| 2.1 基本需求分析                                             |
| ------------------------------------------------------------ |
| wc.exe -x //显示图形界面                                     |
| wc.exe -s //递归处理目录下符合条件的文件(file.c /file.txt)。 |
| wc.exe -a //返回更复杂的数据（代码行 / 空行 / 注释行）       |
| wc.exe -l //返回文件 file.c /file.txt的行数                  |
| wc.exe -w //返回文件file.c /file.txt的词的数目               |
| wc.exe -c //返回文件file.c /file.txt 的字符数                |

### 2.2 实现思路

因为在学校团队使用c++进行开发，对c++比较熟悉，所以选择使用c++进行开发。由于之前从来没有进行过GUI图形界面的开发，一开始并不打算实现高级功能。-l -s -a -w -c都是对文件内容进行处理计算，因此可以针对不同参数的选择对应不同的函数实现，并把函数和计算得出的文件内部属性（行数、字符数等）封装为一个类，属性设置为private，仅允许类函数访问，类函数设置为public，允许外部调用。在程序一开始实例化类对象，然后调用类函数进行处理，避免了多余参数的传递。

后来把基本功能、扩展功能实现完成后考虑进行图形界面的开发，才发现c++可用的图形库并不多（相比于java和python），而且考虑到用高级的图形库进行开发学习时间会较长。经过查阅资料和向同学了解，有种3方案看似可行，第一是用easyxC++图形库开发，easyx面向初学者简单易上手，第二种是用python进行图形界面的开发（我不熟悉java），然后调用已经写好的C++程序，最后一种借用于Qt Creater中内置的Qt designer进行设计。经过考虑，我最后选择了Qt，easyx实现按键事件并没有对应的函数，虽然易上手但是难实现需求；python只能调用c语言程序，调用c++代码要进行复杂的转换和函数封装。而Qt正是为桌面应用而生，借助于布局界面自动生成部分图形代码，使开发周期大大缩短。

## 三、设计实现过程

### 3.1 类和函数

```
class Twc{
private:
    int num_of_characters;//字符数量
    int num_of_words;//词数
    int num_of_rows;//行数
    int num_of_codeLines;//代码行数
    int num_of_blankLines;//空白行数
    int num_of_commentLines;//注释行数
    struct Topt opt;
public:
    Twc(){
        num_of_characters=num_of_words=num_of_rows\
        =num_of_codeLines=num_of_blankLines=num_of_commentLines=0;
        opt.s=opt.c=opt.w=opt.l=opt.a=false;
    };
    void CalRows(FILE *fp);
    void CalCharacters(FILE*fp);
    void CalWords(FILE*fp);
    void CalCodelines(FILE*fp);
    void CalCommentlines(FILE*fp);
    void ListFiles(string path,string mode);
    void Work(char*filename);
    void Choose(int argc,char*argv[]);
};
```

### 3.2 系统流程图

![img](https://img2020.cnblogs.com/blog/1968740/202003/1968740-20200325084855832-1231989184.png)

 

 

## 四、核心代码说明

### 4.1 统计函数

```

bool IsCharacter(char ch){//用于判断除制表符、空格、回车之外可见的字符
    if(ch=='\t'||ch==' '||ch=='\n')return false;
    else return true;
};

bool IsCharacter_(char ch){//用于判断英文字母
    if(ch>='0'&&ch<='9')return true;
    if(ch>='a'&&ch<='z')return true;
    if(ch>='A'&&ch<='Z')return true;
    return false;
};

void Twc::CalRows(FILE *fp){//计算行数
    char flag=0;int count=0;
    while(!feof(fp)){
        flag=fgetc(fp);
        if(flag=='\n')
            count++;
    }
    num_of_rows=count+1;
    cout<<"the number of rows is "<<num_of_rows<<endl;
    rewind(fp);//重置文件指针到文件开头准备进行下一步操作
    return;
}

void Twc::CalCharacters(FILE *fp){//计算字符数
    char flag=0;int count=0;
    while(!feof(fp)){
        flag=fgetc(fp);
        if(IsCharacter(flag))
            count++;
    }
    num_of_characters=count-1;
    cout<<"the number of characters is "<<num_of_characters<<endl;
    rewind(fp);
    return;
}

void Twc::CalWords(FILE*fp){//计算词数
    char str[bufsize];
    int len;//每行的字符数
    int count=0;
    while(!feof(fp)){
        fgets(str,bufsize,fp);
        len=strlen(str);
        for(int i=0;i<len;i++)
        {
            if(IsCharacter_(str[i])){
                if(i==0||!IsCharacter_(str[i-1]))
                    count++;
            }
        }
    }
    num_of_words=count;
    cout<<"the number of words is "<<num_of_words<<endl;
    rewind(fp);
    return;
}

void Twc::CalCodelines(FILE*fp){//计算代码行数和空行
    char str[bufsize];
    int len;
    int show_code,code_line,blank_line;//每行可见字符数
    show_code=code_line=blank_line=0;
    while(!feof(fp)){
        fgets(str,bufsize,fp);
        len=strlen(str);
        for(int i=0;i<len;i++){
            if(IsCharacter(str[i]))
                show_code++;
        }
        if(show_code>1)
            code_line++;
        else
            blank_line++;
        show_code=0;//置零，计算下一行
    }
    num_of_codeLines=code_line;
    num_of_blankLines=blank_line;
    cout<<"the number of code lines is "<<num_of_codeLines<<endl;
    cout<<"the number of blank lines is "<<num_of_blankLines<<endl;
    rewind(fp);
    return;
}

void Twc::CalCommentlines(FILE*fp){//计算注释行
    char str[bufsize];
    int len;//每行的字符数
    int count=0;
    int show_comment=0;
    bool is_commentline= false;
    while(!feof(fp)){
        fgets(str,bufsize,fp);
        len=strlen(str);
        for(int i=0;i<len;i++){
            if(IsCharacter(str[i])){
                show_comment++;
                if(str[i]=='/'){
                    if(i+1<len&&str[i+1]=='/')
                        is_commentline= true;
                    break;
                }
                if(show_comment>1)
                    break;
            }
        }
        if(is_commentline==true)
            count++;
    }
    num_of_commentLines =count;
    cout<<"the number of comment lines is "<<num_of_commentLines<<endl;
    rewind(fp);
    return;
}

void Twc::Work(char *filename) {//运行函数
    if(opt.s== true){
        string mode = filename;
        string path=".\\";
        ListFiles(path,mode);
        return;
    }
    FILE *fp=fopen(filename,"r");
    if(fp==NULL){
        cout<<"open file error!"<<endl;
        cout<<"Press any key to continue"<<endl;
        getchar();
        exit(0);
    }
    if(opt.l==true){
        CalRows(fp);
    }
    if(opt.c== true){
        CalCharacters(fp);
    }
    if(opt.a==true){
        CalCodelines(fp);
        CalCommentlines(fp);
    }
    if(opt.w== true){
        CalWords(fp);
    }
}

void Twc::ListFiles(string path,string mode){//递归处理目录下符合条件的文件
    _finddata_t file;
    intptr_t HANDLE;
    string Onepath = path + mode;
    HANDLE = _findfirst(Onepath.c_str(), &file);
    if (HANDLE == -1L)
    {
        cout << "can not match the folder path" << endl;
        system("pause");
    }
    do {
        //判断是否有子目录
        if (file.attrib & _A_SUBDIR)
        {
            //判断是否为"."当前目录，".."上一层目录
            if ((strcmp(file.name, ".") != 0) \
            && (strcmp(file.name, "..") != 0))
            {
                string newPath = path +"\\" + file.name;
                ListFiles(newPath,mode);
            }
        }
        else
        {
            cout << file.name << " " << endl;
            FILE *fp=fopen(&file.name[0],"r");
            if(fp==NULL){
                cout<<"open file error!"<<endl;
                cout<<"Press any key to continue"<<endl;
                getchar();
                exit(0);
            }
            if(opt.l==true){
                CalRows(fp);
            }
            if(opt.c== true){
                CalCharacters(fp);
            }
            if(opt.a==true){
                CalCodelines(fp);
                CalCommentlines(fp);
            }
            if(opt.w== true){
                CalWords(fp);
            }
            cout<<"------------------------------"<<endl;
        }
    } while (_findnext(HANDLE, &file) == 0);
    _findclose(HANDLE);
}

void Twc::Choose(int argc,char*argv[]){//参数选择
    for(int i=0;i<argc-1;i++){

        if(strcmp(argv[i],"-s")==0){
            opt.s=true;
        }
        if(strcmp(argv[i],"-c")==0){
            opt.c=true;
        }
        if(strcmp(argv[i],"-w")==0){
            opt.w=true;
        }
        if(strcmp(argv[i],"-l")==0){
            opt.l=true;
        }
        if(strcmp(argv[i],"-a")==0){
            opt.a=true;
        }
    }
}
```

### 4.2 Main函数，是否启用图形界面

```
int main(int argc, char *argv[])
{
    char filename[maxlen_filename];
    strcpy(filename,argv[argc-1]);
    Twc filefp;
    filefp.Choose(argc,argv);
    if(filefp.SomethingTrue()){
        filefp.Work(filename);
        return 0;
    }

    QApplication a(argc, argv);
    MainWindow w;
    w.setWindowTitle(QString("word count"));
    w.show();
    w.setAutoFillBackground(true);
    QPalette palette=w.palette();
    palette.setBrush(QPalette::Window,QBrush(QPixmap("C:\\Users\\24277\
    \\Documents\\WC\\a.PNG").scaled(w.size()\
    ,Qt::IgnoreAspectRatio,Qt::SmoothTransformation)));
    w.setPalette(palette);
    return a.exec();
}
```

## 五、测试运行

### 5.1 单文件测试

test.c![img](https://img2020.cnblogs.com/blog/1968740/202003/1968740-20200325030140116-1906914445.png)

运行结果：

![img](https://img2020.cnblogs.com/blog/1968740/202003/1968740-20200325030208012-223845173.png)

### 5.2 多文件测试

文件夹test

![img](https://img2020.cnblogs.com/blog/1968740/202003/1968740-20200325030245859-1574706627.png)

运行结果：

![img](https://img2020.cnblogs.com/blog/1968740/202003/1968740-20200325030259471-1951432402.png)

### 5.3 GUI测试

WC.exe -x打开GUI程序

![img](https://img2020.cnblogs.com/blog/1968740/202003/1968740-20200325030325584-690162163.png)

输入框输入文件路径或点击选择文件按钮调出文件对话框选择文件

![img](https://img2020.cnblogs.com/blog/1968740/202003/1968740-20200325030342924-69633325.png)

连续查询多个文件

![img](https://img2020.cnblogs.com/blog/1968740/202003/1968740-20200325030356598-264779726.png)

## 六、项目小结

通过这次个人项目，我意识到软件工程学科的重要性（因为开始没有规划GUI扩展导致最后把项目的代码重写了一遍，分出了非GUI界面wc和GUI界面wc两个项目）。软件工程开始时的整体规划对项目的影响非常大，随着我们学习的深入，我们应该多花时间在分析规则上，从而减少开发、debug的时间。借助本次项目，我也学习到了图形界面的开发、main参数的使用、c与python之间的相互调用等，十分难得。
