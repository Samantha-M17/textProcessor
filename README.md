# Thoughtworks2019届校招作业题

作者：马欣睿<br>
学校：西安电子科技大学<br>
学历：硕士<br>
学院：人工智能学院<br>
专业：电路与系统<br>
导师：石光明<br>

## 作业内容

文本处理是非常常见但又非常重要的任务。其中操作纷繁复杂。而今天我们的目标就是制作一个小型的文本预处理器。其主要功能就是对文本进行预处理以便后续进行固定宽度的排版。为了方便说明，我们定义如下的概念：<br>
* 空白字符（white space）：指空格 ' '。
* 文本字符（character）：指大写或者小写的英文字母。
* 节（segment）：指一串（大于或者等于一个）连续的空白字符或者文本字符<br>
<br>
我们的文本处理器的功能还很朴素，无法处理除 空白字符 和 文本字符 之外的字符。<br>
我们将会使用一个固定长度作为宽度进行排版。大于这个宽度的字符会被折行。而折行不会显示任何连字符（例如 “-”），也无需对 空白字符 进行额外处理。例如，假设我们规定最大宽度为30，则 "The main theme of education in engineering school is learning toteach yourself" 将排版为：<br>
The main theme of education in<br>
engineering school is learnin<br>
g to teach yourself

### 要求

现在请书写一个函数，该函数的输入为两个参数：<br>
* 需要处理的文本
* 排版宽度<br>
该函数的返回值为预处理后的文本。预处理后的文本为每一节，及其所在的行号。中间以分号隔开。若一个节跨越了多行，则行号用逗号隔开，并从小到大进行排列。<br>

例如，假设输入为：The main theme of education in engineering school is learning to teach yourself，并且排版宽度指定为 30，则返回：
```
The(1); (1);main(1); (1);theme(1); (1);of(1); (1);education(1); (1);in(1); (2);engineering(2); (2);school(2); (2);is(2); (2);learning(2,3); (3);to(3); (3);teach(3); (3);yourself(3);
```
又例如，假设输入为："So many whitespaces"，而排版宽度为 10，则返回：
```
So(1); (1);many(1); (1);whitespaces(2,3);
```

### 使用语言

java

## 实现代码

```java
public class TextProcessor {
	public String process(String text, int width) {
		 StringBuffer sb = new StringBuffer(); //创建字符缓冲区
		 int row_n = 1; //初始化行数
		 int i=-1;
		 int word_start=0; //初始化单词节首字符索引值
		 int space_start = 0; //初始化空格节首字符索引值
		 boolean flag1 = false; //初始化单词节标识符为false，当单词节出现跨行则此标识符为true
		 boolean flag2 = false; //初始化空格节标识符为false，当空格节出现跨行则此标识符为true
		 if (width<10 || width>80) {
			 throw new RuntimeException("ERROR:Width out of range");
		 }
		 while((i++)<text.length()-1) {
	        	if((text.charAt(i) >= 'A' && text.charAt(i) <= 'Z') || (text.charAt(i) >= 'a' && text.charAt(i) <= 'z') || (text.charAt(i) >= '0' && text.charAt(i) <= '9') || text.charAt(i) ==' ') {
	        		if((text.charAt(i) >= 'A' && text.charAt(i) <= 'Z') || (text.charAt(i) >= 'a' && text.charAt(i) <= 'z') || (text.charAt(i) >= '0' && text.charAt(i) <= '9') ) {  //若字符为字母或数字，则为单词节
	        				sb.append(text.charAt(i));
	        				if (i+1<text.length()) {
	        					if ((i+1) % width == 0 && ((text.charAt(i+1) >= 'A' && text.charAt(i+1) <= 'Z') || (text.charAt(i+1) >= 'a' && text.charAt(i+1) <= 'z')|| (text.charAt(i+1) >= '0' && text.charAt(i+1) <= '9') )) {
	        					row_n++;
	        					flag1 = true;
	        				    } //当前字符位于单词节中间（当前字符为单词节第一个至倒数第二个字符）时，若出现跨行，则将标志位flag1的值改为true,行号加一
	        					if(text.charAt(i+1)==' ') {
	        						space_start = i+1;
	        						if(flag1) {
	        	        				sb.append('(');
	        	        				for(int a = row_n-(1+word_start/width);a>0;a--) {
	        	        					sb.append(row_n-a);
	        	        					sb.append(',');
	        	        				} 
	        	        				sb.append(row_n);
	        	        				sb.append(')');
	        	        				sb.append(';');// 跨行时，将行号从小到大依次输出
	        	        				flag1 = false;
	        	        			}else {
	        	        				sb.append('(');
	                					sb.append(row_n);
	                					sb.append(')');
	        	        				sb.append(';');
	        	        			}
	        				}
	        		}
	        		}
	        		if(text.charAt(i) ==' ') {  //若字符为空格，则为空格节
	        			sb.append(text.charAt(i));
	        			if ((i+1) % width == 0 && text.charAt(i+1)==' ' ) {
       					row_n++;
       					flag2 = true;
       				    } //当前字符位于空格节中间（当前字符为空格节第一个至倒数第二个字符）时，若出现跨行，则将标志位flag2的值改为true,行号加一
       				if((text.charAt(i+1) >= 'A' && text.charAt(i+1) <= 'Z') || (text.charAt(i+1) >= 'a' && text.charAt(i+1) <= 'z')|| (text.charAt(i+1) >= '0' && text.charAt(i+1) <= '9') ) {
       						word_start = i+1;
       					if(flag2) {
       	        				sb.append('(');
       	        				for(int a = row_n-(1+space_start/width);a>0;a--) {
       	        					sb.append(row_n-a);
       	        					sb.append(',');
       	        				}  
       	        				sb.append(row_n);
       	        				sb.append(')');
       	        				sb.append(';');// 跨行时，将行号从小到大依次输出
       	        				flag2 = false;
       	        			}else {
       	        				sb.append('(');
               					sb.append(row_n);
               					sb.append(')');
       	        				sb.append(';');
       	        			}
       				}
	        		}
	        	}else {
	        		throw new RuntimeException("ERROR: Invalid character detected!");
	        	}
	        	
	        }
		 if(flag1) {
				sb.append('(');
				for(int a = row_n-(1+word_start/width);a>0;a--) {
					sb.append(row_n-a);
					sb.append(',');
				}
				sb.append(row_n);
				sb.append(')');
				sb.append(';');
				
			}else {
				sb.append('(');
				sb.append(row_n);
				sb.append(')');
				sb.append(';');
			}
		  return sb.toString();
}
}
```

### 如何构建并运行代码

将上述代码中构建的函数

```
public String process(String text, int width)
```
在main函数中进行调用即可，示例如下：
```java
import java.util.Scanner;
public class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        String str = sc.nextLine();
        int width = sc.nextInt();
        System.out.println(process(str,width));
 
        }
    
    
    public static String process(String text, int width) {
		 StringBuffer sb = new StringBuffer();
		 int row_n = 1;
		 int i=-1;
		 int word_start=0;
		 int space_start = 0;
		 boolean flag1 = false;
		 boolean flag2 = false;
		 if (width<10 || width>80) {
			 throw new RuntimeException("ERROR:Width out of range");
		 }
		 while((i++)<text.length()-1) {
	        	if((text.charAt(i) >= 'A' && text.charAt(i) <= 'Z') || (text.charAt(i) >= 'a' && text.charAt(i) <= 'z') || (text.charAt(i) >= '0' && text.charAt(i) <= '9') || text.charAt(i) ==' ') {
	        		if((text.charAt(i) >= 'A' && text.charAt(i) <= 'Z') || (text.charAt(i) >= 'a' && text.charAt(i) <= 'z') || (text.charAt(i) >= '0' && text.charAt(i) <= '9') ) { 
	        				sb.append(text.charAt(i));
	        				if (i+1<text.length()) {
	        					if ((i+1) % width == 0 && ((text.charAt(i+1) >= 'A' && text.charAt(i+1) <= 'Z') || (text.charAt(i+1) >= 'a' && text.charAt(i+1) <= 'z')|| (text.charAt(i+1) >= '0' && text.charAt(i+1) <= '9') )) {
	        					row_n++;
	        					flag1 = true;
	        				    }
	        					if(text.charAt(i+1)==' ') {
	        						space_start = i+1;
	        						if(flag1) {
	        	        				sb.append('(');
	        	        				for(int a = row_n-(1+word_start/width);a>0;a--) {
	        	        					sb.append(row_n-a);
	        	        					sb.append(',');
	        	        				}
	        	        				sb.append(row_n);
	        	        				sb.append(')');
	        	        				sb.append(';');
	        	        				flag1 = false;
	        	        			}else {
	        	        				sb.append('(');
	                					sb.append(row_n);
	                					sb.append(')');
	        	        				sb.append(';');
	        	        			}
	        				}
	        		}
	        		}
	        		if(text.charAt(i) ==' ') {
	        			sb.append(text.charAt(i));
	        			if ((i+1) % width == 0 && text.charAt(i+1)==' ' ) {
        					row_n++;
        					flag2 = true;
        				    }
        				if((text.charAt(i+1) >= 'A' && text.charAt(i+1) <= 'Z') || (text.charAt(i+1) >= 'a' && text.charAt(i+1) <= 'z')|| (text.charAt(i+1) >= '0' && text.charAt(i+1) <= '9') ) {
        						word_start = i+1;
        					if(flag2) {
        	        				sb.append('(');
        	        				for(int a = row_n-(1+space_start/width);a>0;a--) {
        	        					sb.append(row_n-a);
        	        					sb.append(',');
        	        				}
        	        				sb.append(row_n);
        	        				sb.append(')');
        	        				sb.append(';');
        	        				flag2 = false;
        	        			}else {
        	        				sb.append('(');
                					sb.append(row_n);
                					sb.append(')');
        	        				sb.append(';');
        	        			}
        				}
	        		}
	        	}else {
	        		throw new RuntimeException("ERROR: Invalid character detected!");
	        	}
	        	
	        }
		 if(flag1) {
				sb.append('(');
				for(int a = row_n-(1+word_start/width);a>0;a--) {
					sb.append(row_n-a);
					sb.append(',');
				}
				sb.append(row_n);
				sb.append(')');
				sb.append(';');
				
			}else {
				sb.append('(');
				sb.append(row_n);
				sb.append(')');
				sb.append(';');
			}
		  return sb.toString();
}
}

```
* 输入：两行内容，第一行为待排版的文本，第二行为排版宽度
* 输出：按作业要求输出<br>

### 代码构建思路
本作业代码构建思路为：<br>
1.遍历字符串<br>
2.若为字母或数字则输出，检查每个字符的索引值，若当前字符索引值为宽度的倍数，且当前字符位于单词节中间（第一位至倒数第二位），则认为单词节跨行，设置标志位flag1为true。若下一字符为空格，则输出行号：1.若flag1为true，则从小到大依次输出行号；2.若flag1为false，则直接输出当前行号<br>
3.若为空格则输出，检查每个字符的索引值，若当前字符索引值为宽度的倍数，且当前字符位于空格节中间（第一位至倒数第二位），则认为空格节跨行，设置标志位flag2为true。若下一字符为单词字符（字母或数字），则输出行号：1.若flag2为true，则从小到大依次输出行号；2.若flag2为false，则直接输出当前行号<br>

## Author

* 马欣睿
独自完成

## 运行环境
jdk-10.0.2
