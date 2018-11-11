---
ID: 3800
post_title: 'Golang &#8211; A &#8216;hello world&#8217; example'
author: sher
post_excerpt: "Here's a quick hello world example. "
layout: post
permalink: >
  https://codingbee.net/tutorials/golang/golang-a-hello-world-example
published: true
post_date: 2018-08-11 13:43:03
---
Once you've <a href="http://codingbee.net/tutorials/golang/installing-golang">installed Golang</a> and <a href="http://codingbee.net/tutorials/golang/setup-a-workspace-for-go">set up your workspace</a>, you're now ready to write your hello world go program. Your Go code has to be written in the src folder, and the file needs to have the '.go' extension. So I created the following file:


<pre>

$ cat hello.go
package main

// this is a comment. 
import "fmt"


func main() {
    fmt.Println("hello world")
} 
</pre>


There are 2 types of Go projects you can create. One is a shared library project which is code that's used as part of other Golang projects. The other is where the end product of a Golang project is a standalone executable binary.  

The <code>package main</code> tells Golang that this project will be creating a standalone executable binary. Next, we declared a function called 'main'. You can choose what to call your function, however naming a function 'main' has special meaning in Golang, it tells Golang what is the first block of code to execute when the binary gets called. The binary exits out once the code in the main function finishes executing. 

Golang comes builtin with a collection of packages. This collection is known as the <a href="https://golang.org/pkg/#stdlib">standard library</a>. 

In our example, we've called another function called '<a href="https://golang.org/pkg/fmt/#Println">Println</a>'. Println is basically a function that print's content to the standard output. This function is defined inside the <a href="https://golang.org/pkg/fmt/">fmt</a> package. fmt is one of the standard library packages. The import declaration is how we load in the fmt package so that we can call its function in our code.


Now we can test our code by running:

<pre>
$ go run hello.go
hello world
</pre>

This 'run' command performed a number of tasks behind the scenes. First, it created a binary from our code,  executed that binary, then deleted that binary. 

Now that has worked, let's now create the binary. First we need to set the GOBIN variable which tells Golang where to store the binary end product to:


<pre>
$ export GOBIN="$GOPATH/bin"
</pre>


Then while in the src folder, run the install command:


<pre>
$ go install hello.go
</pre>


This ends up compiling the binary and then saving it to the GOBIN folder:

<pre>
$ echo $GOBIN
/root/go_project/bin
$ ll $GOBIN
total 1968
-rwxr-xr-x. 1 root root 2011612 Aug 12 01:30 hello
$ file /root/go_project/bin/hello
/root/go_project/bin/hello: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), statically linked, not stripped

</pre>

We can now run the standalone binary:


<pre>
$ /root/go_project/bin/hello
hello world
</pre>

We had to specify the full path of the binary, but if you want to run this binary like any other command then just move it to one of the folders specified in the PATH environment variable.