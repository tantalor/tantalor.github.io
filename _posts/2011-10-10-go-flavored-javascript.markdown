---
author: John Tantalo
comments: true
date: 2011-10-10 08:20:09+00:00
layout: post
slug: go-flavored-javascript
title: Go-flavored JavaScript
wordpress_id: 203
---

This week I learned a little bit of [Go](http://en.wikipedia.org/wiki/Go_(programming_language)). I was fascinated by the power and simplicity of goroutines and channels.



With these ideas fresh in my mind, I decided to reproduce Mark C. Chu-Carroll's [Go prime sieve](http://scienceblogs.com/goodmath/2009/11/the_go_i_forgot_concurrency_an.php) in JavaScript with goroutines and channels as my toolset instead of conventional JavaScript idioms. **[Find my translation on GitHub.](https://github.com/tantalor/gojs)**



I chose JavaScript because it shares many traits with Go, such as multiple threads of control, but doesn't have a notion of channels.



## Background: goroutines and channels





A _goroutine_ is an independent concurrent thread of control.



A _channel_ is a mechanism for two concurrently executing functions to communicate by passing a value. A channel has two methods, _read_ and _write_, which are synchronous by default. That is, a _write_ blocks until there is a _read_ to consume it, and a _read_ blocks until there is a _write_ to consume.



## JavaScript implementation





Goroutines are rather trivial to implement, a simple _setTimeout_ will schedule an independent thread of control.



>     function go (fn) {
>       setTimeout(fn, 0);
>     }; 





Synchronous channels will require a queue of _reader_ and _writer_ callbacks. I decided to call the reader callbacks before the writers, but I don't think it matters.



>     var chan = function () {
>       this.readers = []; // [cb, ...]
>       this.writers = []; // [[value, cb], ...]
>     };
>     
>     chan.prototype.read = function (cb) {
>       if (this.writers.length) {
>         // consume a writer
>         var writer = this.writers.shift();
>         cb(writer[0]);
>         writer[1](writer[0]);
>       } else {
>         // queue the reader
>         this.readers.push(cb);
>       }
>     };
>     
>     chan.prototype.write = function (value, cb) {
>       if (this.readers.length) {
>         // consumer a reader
>         var reader = this.readers.shift();
>         reader(value);
>         cb(value);
>       } else {
>         // queue the writer
>         this.writers.push([value, cb]);
>       }
>     }; 



I also pass the writer's value to its callback because it could be useful.



## The prime sieve



Mark's Go sieve begins with an integer generator.



>     func generate_integers() chan int {
>         ch := make(chan int);
>         go func(){
>             for i := 2; ; i++ {
>                 ch <- i;
>             }
>          }();
>         return ch;
>     } 





The problem here is that the channel write (`ch <- i`) blocks inside of a loop. In JavaScript, we "block" by passing a callback that is called once the procedure can continue. Here, the _producer_ function passes itself as the callback to _write_.



>     function integers () {
>       var ch = new chan();
>       go(function () {
>         var producer = function (i) {
>           ch.write(i + 1, producer);
>         };
>         ch.write(2, producer);
>       });
>       return ch;
>     }; 





This Go function excludes multiples of a given prime from the given channel.



>     func filter_multiples(in chan int, prime int) chan int {
>        out := make(chan int);
>        go func() {
>           for {
>              if i := <- in; i % prime != 0 {
>                  out <- i;
>              }
>           }
>         }(); 
>        return out; 
>     }
>      





We can rewrite this with another recursive callback, but this time we only have to block when we do a write.



>     function filter_multiples (ch, prime) {
>       var out = new chan();
>       go(function () {
>           var consumer = function (i) {
>             if (i % prime != 0) {
>               out.write(i, function () {
>                 ch.read(consumer);
>               });
>             } else {
>               ch.read(consumer);
>             }
>           }
>           ch.read(consumer);
>       });
>       return out; 
>     };
>      





The sieve in Go will chain a series of channels to exclude multiples of all the primes we have seen.



>     func sieve() chan int {
>        out := make(chan int);
>        go func() {
>           ch := generate_integers();
>           for {
>     	     prime := <- ch;
>     	     out <- prime;
>     	     ch = filter_multiples(ch, prime);
>           }	
>        }();
>        return out;
>     } 





We achieve the same in JavaScript with another recursive callback.



>     function sieve () {
>        var out = new chan();
>        go(function () {
>           var ch = integers();
>           function iteration () {
>             ch.read(function (prime) {
>               out.write(prime, function () {
>                 ch = filter_multiples(ch, prime);
>                 iteration();
>               });
>             });
>           };
>           iteration();
>        });
>        return out;
>     }; 





Mark's program simply reads from the sieve channel and prints out each prime number.



>     func main() {
>       primes := sieve();
>       for {
>         fmt.Println(<-primes);
>       }
>     } 





Mine uses another recursive callback to do the same.



>     function main () {
>       var primes = sieve();
>       function iteration() {
>         primes.read(function (i) {
>           sys.puts(i);
>           iteration();
>         });
>       };
>       iteration();
>     }
>     
>     main(); 





## Demo





[Go-flavored JavaScript: Prime Sieve](http://github.johntantalo.com/gojs/)





## Discussion





Note that the call to _main()_ returns (practically) immediately, since the goroutine in _sieve()_, which hasn't executed, has not yet written anything to the channel, so the _read_ inside _iteration_ just pushes the callback onto a read queue and returns. Once the main thread of control stops, the event loop begins running the goroutines, which drive the rest of the program. (Please correct me if I'm wrong.)



That functions return quickly is generally desirable, in JavaScript or Go, because a caller should not have to wait for a function to do some "hard work" before it returns. Instead, the hard work should occur in a separate thread of control and passed back to the caller by some layer of indirection.



JavaScript programmers typically solve this problem with callbacks which receive result of the hard work. The Go language offers synchronous channels between independent threads of control as a more sophisticated solution. These tools are easily ported to idiomatic JavaScript based on callbacks. Although unconventional, these tools are simple and may be very powerful when composed.



I hope this toy example borrowed from Go inspires JavaScript programmers to consider the unconventional.

