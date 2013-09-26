% Introducing Erlang
% Bob Ippolito
% September 26, 2013

# What do I know?

- Erlang user since 2006
- Founded Mochi Media, Inc. (CTO from 2005-2012)
- Developed popular Erlang web server (mochiweb)

# Erlang

* **Er**icsson **lang**uage

![Agner Krarup **Erlang**](img/Erlang.jpg)

# Raison d'être

- Came from Ericsson R&D in the 80s
- Telecom shifting to packet switching
- Multi-service networks
- Improve development of telephony applications
- Scalable, distributed, no down time, fast development

# Domain

*Distributed massively concurrent soft-realtime systems that do not stop.*

#

<section id="applications">
<h1>Applications</h1>

- Networking (Switching, Routing, SDN)
- Messaging (SMS, MMS, IM) services
- Distributed systems (Databases, cluster management)
- Web services (analytics, ad servers)
- Simulation (testing)
- Game servers
</section>

<section id="app-telecom" class="logos">
<h2>Telecom</h2>

<img src="img/ericsson-logo.svg" id="ericsson-logo" alt="Ericsson"/>
<img src="img/nokia-logo.svg" id=nokia-logo" alt="Nokia"/>
<img src="img/att-logo.svg" id="att-logo" alt="AT&amp;T"/>
<br/>
<img src="img/motorola-logo.svg" id="motorola-logo" alt="Motorola"/>
<img src="img/bt-logo.svg" id="bt-logo" alt="BT"/>
<img src="img/t-mobile-logo.svg" id="t-mobile-logo" alt="T-Mobile"/>

</section>

<section id="app-erlang" class="logos">

<img src="img/erlang-solutions-logo.svg" id="erlang-solutions-logo" alt="Erlang Solutions"/>

- Conferences
- Training
- Certification
- Consulting
- Support
</section>

<section id="app-db" class="logos">
<h2>Database Vendors</h2>

<img src="img/basho-logo.svg" id="basho-logo" alt="Basho"/>
<img src="img/cloudant-logo.png" id="cloudant-logo" alt="Cloudant"/>
</section>

<section id="app-ads">
<h2>Advertising</h2>

- Mochi Media
- AdRoll
- OpenX
- AdGear
- AOL
</section>

<section id="app-cloud">
<h2>Cloud / Data Center Automation</h2>

- Rackspace
- Opscode
- Heroku
- VMWare
</section>

<section id="app-mobile">
<h2>Mobile/VoIP services</h2>

- WhatsApp
- 2600hz
- TigerText
</section>

<section id="app-monitoring">
<h2>Monitoring / Ops</h2>

- Boundary
- BugSense
- Alert Logic
</section>

<section id="app-media" class="logos">
<h2>Media</h2>

<img src="img/the-boston-globe-logo.svg" id="the-boston-globe-logo" alt="The Boston Globe"/>
<br/>
<img src="img/the-huffington-post-logo.gif" id="the-huffington-post-logo" alt="The Huffington Post"/>
</section>

<section id="app-games">
<h2>Games</h2>

- Spilgames
- Wooga
- MuchDifferent / Pikkotekk
- Linden Lab
</section>

<section id="app-money">
<h2>Money</h2>

- Klarna (payments)
- Smarkets (sports betting)
</section>

<section id="app-open-source">
<h2>Open Source</h2>

- Riak (Basho)
- CouchDB (CouchBase, Cloudant)
- ejabberd (Process-One)
- RabbitMQ (LShift &rarr; VMWare)
</section>

# Not good for

- Client side applications (GUIs)
- … but web-based UIs work well
- Number crunching

#

<section id="ancestry">
<h1>Ancestry</h1>

- Syntax mostly from Prolog and ML
- List comprehensions from Miranda
- Message passing inspired by Smalltalk
</section>

<section id="prolog-roots">
Erlang was prototyped in Prolog:
```prolog
% factorial.pl (Prolog)
:- module(factorial, [factorial/2]).

factorial(0, 1).
factorial(N, F) :-
  N>0,
  N1 is N-1,
  factorial(N1, F1),
  F is N * F1.
```
```erlang
% factorial.erl (Erlang)
-module(factorial).
-export([factorial/2]).

factorial(0) -> 1;
factorial(N) when N > 0 ->
  N * factorial(N - 1).
```
</section>

<section id="miranda-roots">
List comprehensions from Miranda (like Haskell)
```haskell
-- Subsets.hs
module Subsets (subsets) where

subsets :: [a] -> [[a]]
subsets []     = [[]]
subsets (x:xs) = [(x:y) | y <- ys] ++ ys
  where ys = subsets xs
```
```erlang
% subsets.erl
-module(subsets).
-export([subsets/1]).

subsets([]) ->
    [[]];
subsets([X | Xs]) ->
    Ys = subsets(Xs),
    [[X | Y] || Y <- Ys] ++ Ys.
```
</section>

<section id="ml-roots">
Pattern matching like ML
```ocaml
(* reverse.sml *)
fun reverse(l : 'a list) : 'a list =
    reverse1(l, [])

fun reverse1(l : 'a list, acc : 'a list) =
    case l of
        [] => acc
      | (x::xs) => reverse1(xs, x::acc)
```
```erlang
% reverse.erl
-module(reverse).
-export([reverse/1]).

reverse(L) ->
    reverse(L, []).

reverse(L, Acc) ->
    case L of
        [] -> Acc;
        [X | Rest] -> reverse(Rest, [X | Acc])
    end.
```
</section>

<section id="smalltalk-roots">
Smalltalk didn't really use concurrency for actors or
message passing, so the relationship is mostly conceptual.
</section>

# Key Features

- Declarative
- Concurrent
- Multi-core
- Distributed
- Robust
- Versatile

#

<section id="declarative">
<h1>Declarative</h1>

* "Describe the problem, not the solution"
* Great syntax for this (but not C-like)!
* Functional code is (often) easier to understand and test
* … but not 100% pure (message passing is a side-effect)
</section>

<section id="merge-sort">

```erlang
-module(merge_sort).
-export([merge_sort/1]).

% bottom-up merge sort
merge_sort([]) ->
    [];
merge_sort(L) ->
    iterate([[X] || X <- L]).

iterate([Xs]) ->
    Xs;
iterate(Lists) ->
    iterate(merge_lists(Lists)).

merge_lists([Xs, Ys | Rest]) ->
    [merge2(Xs, Ys) | merge_lists(Rest)];
merge_lists(Lists) ->
    Lists.

merge2(Xs=[X | _], [Y | Ys]) when X > Y ->
    [Y | merge2(Xs, Ys)];
merge2([X | Xs], Ys) ->
    [X | merge2(Xs, Ys)];
merge2([], Ys) ->
    Ys.
```
</section>

<section id="merge-sort-python">
```python
# merge_sort.py
def merge_sort(lst):
    if not lst:
        return []
    lists = [[x] for x in lst]
    while len(lists) > 1:
        lists = merge_lists(lists)
    return lists[0]

def merge_lists(lists):
    result = []
    for i in range(0, len(lists) // 2):
        result.append(merge2(lists[i*2], lists[i*2 + 1]))
    if len(lists) % 2:
        result.append(lists[-1])
    return result

def merge2(xs, ys):
    i = 0
    j = 0
    result = []
    while i < len(xs) and j < len(ys):
        x = xs[i]
        y = ys[j]
        if x > y:
            result.append(y)
            j += 1
        else:
            result.append(x)
            i += 1
    result.extend(xs[i:])
    result.extend(ys[j:])
    return result
```
</section>

<section id="parse-tcp">
Convenient bit syntax for parsing binary data
```erlang
%% This parses a TCP packet header!
<< SourcePort:16, DestinationPort:16, SequenceNumber:32,
   AckNumber:32, DataOffset:4, _Reserved:4, Flags:8,
   WindowSize:16, Checksum:16, UrgentPointer:16,
   Payload/binary >> = Segment,
OptSize = (DataOffset - 5)*32,
<< Options:OptSize, Message/binary >> = Payload,
<< CWR:1, ECE:1, URG:1, ACK:1, PSH:1,
   RST:1, SYN:1, FIN:1>> = <<Flags:8>>,

%% Can now process the Message according to the 
%% Options (if any) and the flags CWR, …, FIN etc.
```
</section>

#

<section id="concurrent">
<h1>Concurrent</h1>

* Erlang concurrency is cheap
* Per-process heaps with incremental GC
* Message passing, not mutexes
* Straightforward control flow (not callbacks!)
</section>

<section id="cost-of-concurrency">
RAM footprint per unit of concurrency (approx)

<table id="concurrency-table">
<tr class="haskell">
    <td class="num">1.3KB</td>
    <td class="name">
        <div class="bar-ctr"><div class="bar"></div></div>
        <span>Haskell ThreadId + MVar (GHC 7.6.3, 64-bit)</span>
    </td>
</tr>
<tr class="erlang">
    <td class="num">2.6 KB</td>
    <td class="name">
        <div class="bar-ctr"><div class="bar"></div></div>
        <span>Erlang process (64-bit)</span>
    </td>
</tr>
<tr class="go">
    <td class="num">4.0 KB</td>
    <td class="name">
        <div class="bar-ctr"><div class="bar"></div></div>
        <span>Go goroutine</span>
    </td>
</tr>
<tr class="java-min">
    <td class="num">64.0 KB</td>
    <td class="name">
        <div class="bar-ctr"><div class="bar"></div></div>
        <span>Java thread stack (minimum)</span>
    </td>
</tr>
<tr class="c-min">
    <td class="num">64.0 KB</td>
    <td class="name">
        <div class="bar-ctr"><div class="bar"></div></div>
        <span>C pthread stack (minimum)</span>
    </td>
</tr>
<tr class="placeholder"><td colspan="2"><hr/></td></td>
<tr class="java">
    <td class="num">1 MB</td>
    <td class="name">
        <div class="bar-ctr"><div class="bar"></div></div>
        <span>Java thread stack (default)</span>
    </td>
</tr>
<tr class="c">
    <td class="num">8 MB</td>
    <td class="name">
        <div class="bar-ctr"><div class="bar"></div></div>
        <span>C pthread stack (default, 64-bit Mac OS X)</span>
    </td>
</tr>
</table>
</section>

<section id="per-process-heaps">
<h2>Per-process heaps</h2>

* No sharing
* GC is per-process, and not "stop the world"!
* Process references do not prevent GC
* Explicitly hibernate idle processes for compaction
</section>

<section id="message-passing">
<h3>RPC with a Counter process</h3>

```erlang
Counter ! {self(), {add, 1}},
receive
  {Counter, {result, N}} ->
    io:format("~p~n", [N])
end
```
</section>

#

<section id="multi-core">
<h1>Multi-core</h1>

* One scheduler per core, scales well to 32+ cores
* Better scalability to more cores is in-progress
* Schedulers understand IO (disk, network calls)
* No implicit synchronization
</section>

<section id="scheduler">
<object type="image/svg+xml" data="img/scheduler.svg"></object>
</section>

<section id="parse-http">
```erlang
%% Parse HTTP headers from Socket
headers(Socket, Request, Headers) ->
    ok = inet:setopts(Socket, [{active, once}]),
    receive
        {http, _, http_eoh} ->
            %% All of the HTTP headers are parsed
            handle_request(Socket, Request, Headers);
        {http, _, {http_header, _, Name, _, Value}} ->
            headers(Socket, Request, [{Name, Value} | Headers]);
        {tcp_closed, _} ->
            exit(normal);
        _Other ->
            %% Invalid request
            exit(normal)
    after ?HEADERS_RECV_TIMEOUT ->
        exit(normal)
    end.
```
</section>

#
<section id="distributed">
<h1>Distributed</h1>

* Built-in distributed computing support
* Easy to get started, "no configuration"
* Same syntax, similar semantics, whether local or not
* Necessary for true fault tolerant systems (2+ nodes)
* Scales well to hundreds of nodes (LAN only)
</section>

<section id="distributed-example">
Connect to other nodes by name
```erlang
(node1@res)1> net_adm:ping(node2@res).
pong
(node1@res)2> self().
<0.38.0>
(node1@res)3> rpc:call(node2@res, erlang, self, []).
<6943.43.0>
(node1@res)4> nodes().
[node2@res]
(node1@res)5> rpc:cast(node2@res, init, stop, []).
true
(node1@res)6> nodes().
[]
```
</section>

<section id="distributed-diagram">
<svg viewBox="0 0 1000 1000" class="full diagram">
  <g class="host" transform="translate(20,20)" data-host="alto">
      <rect width="460" height="900"/>
      <text x="230" y="1em">alto</text>
      <g class="node" data-node="alice@alto" transform="translate(30, 100)">
        <rect width="400" height="650"/>
        <text x="200" y="1em">alice@alto</text>
        <g class="worker" data-name="PidA" transform="translate(200,170)">
            <circle r="100"/>
            <text>PidA</text>
        </g>
        <g class="supervisor" data-name="net_kernelA" transform="translate(200,470)">
            <line x1="0" y1="100" x2="0", y2="230" class="tcp" data-link="net_kernelA epmdA"/>
            <line x1="70.71" y1="70.71" x2="300", y2="230" class="tcp phase1" data-link="net_kernelA epmdB"/>
            <line x1="100" y1="0" x2="400", y2="0" class="tcp phase2" data-link="net_kernelA net_kernelB"/>
            <circle r="100"/>
            <text>net_kernel</text>
        </g>
      </g>
      <g class="epmd" transform="translate(30, 800)">
        <rect width="400" height="60"/>
        <text x="200" y="1em">epmd</text>
      </g>
  </g>
  <g class="host" transform="translate(520,20)" data-host="onyx">
      <rect width="460" height="900"/>
      <text x="230" y="1em">onyx</text>
      <g class="node" data-node="bob@onyx" transform="translate(30, 100)">
        <rect width="400" height="650"/>
        <text x="200" y="1em">bob@onyx</text>
        <g class="worker" data-name="PidB" transform="translate(200,170)">
            <circle r="100"/>
            <text>PidB</text>
        </g>
        <g class="supervisor" data-name="net_kernelB" transform="translate(200,470)">
            <line x1="0" y1="100" x2="0", y2="230" class="tcp" data-link="net_kernelB epmdB"/>
            <circle r="100"/>
            <text>net_kernel</text>
        </g>
      </g>
      <g class="epmd" transform="translate(30, 800)">
        <rect width="400" height="60" data-name="epmdB"/>
        <text x="200" y="1em">epmd</text>
      </g>
  </g>
</svg>
</section>

#

<section id="robust">
<h1>Robust</h1>

* OTP framework encodes these best practices
* Processes can be linked for exit signal propagation
* Errors are localized and do not corrupt application state
* Supervisor processes start/stop/monitor other processes
* "Let it crash" means far less error handling code
</section>

<section id="otp">
<h1>OTP</h1>
Open Telephony Platform

Framework has little to do with telephony.
</section>

<section id="supervisor">
<svg viewBox="0 0 1000 1000" class="full diagram">
  <line x1="430" x2="350" y1="220" y2="400" data-link="SupervisorA WorkerA"/>
  <line x1="570" x2="650" y1="220" y2="400" data-link="SupervisorA SupervisorB"/>
  <line x1="580" x2="400" y1="570" y2="700" data-link="SupervisorB WorkerB1"/>
  <line x1="650" x2="650" y1="600" y2="700" data-link="SupervisorB WorkerB2"/>
  <line x1="720" x2="900" y1="570" y2="700" data-link="SupervisorB WorkerB3"/>
  <g class="supervisor" transform="translate(500, 150)" data-name="SupervisorA">
      <circle r="100"></circle>
      <text>SupervisorA</text>
  </g>
  <g class="worker" transform="translate(350, 500)" data-name="WorkerA">
      <circle r="100"></circle>
      <text>WorkerA</text>
  </g>
  <g class="supervisor" transform="translate(650, 500)" data-name="SupervisorB">
      <circle r="100"></circle>
      <text>SupervisorB</text>
  </g>
  <g class="worker" transform="translate(400, 800)" data-name="WorkerB1">
      <circle r="100"></circle>
      <text>WorkerB1</text>
  </g>
  <g class="worker" transform="translate(650, 800)" data-name="WorkerB2">
      <circle r="100"></circle>
      <text>WorkerB2</text>
  </g>
  <g class="worker" transform="translate(900, 800)" data-name="WorkerB3">
      <circle r="100"></circle>
      <text>WorkerB3</text>
  </g>
</svg>
</section>

# Versatile

* Upgrade/fix live systems with hot code loading
* Debug/profile live systems with tracing
* Easily attach a console to a live system
* Good support for instrumentation

#

<section id="language-overview">
<h2>Language Overview</h2>
</section>

<section id="functional">
<h2>Functional</h2>
* No mutable data structures
* Linked lists (like LISP)
* All state is on the stack
* Tail-call elimiation
</section>

<section id="basic-types" class="syntax">
<dl>
<dt>atom (interned string, like LISP)</dt>
<dd>
```erlang
this_is_an_atom
'also an atom'
```
</dd>
<dt>binary (contiguous array of bytes w/ O(1) slice)</dt>
<dd>
```erlang
<<1, 2, "bytes", 9999:64/little>>
```
</dd>
<dt>integer (unbounded), float (64-bit double)</dt>
<dd>
```erlang
1125899839733759
2.5
```
</dd>
<dt>ref (unique reference)</dt>
<dd>
```erlang
1> make_ref().
#Ref<0.0.0.30>
```
</dd>
</dl>
</section>

<section id="collections" class="syntax">
<h2>Collections</h2>
<dl>
<dt>Lists</dt>
<dd>
```erlang
[1, 2, 3] = [1 | [2 | [3 | []]]]
```
</dd>
<dt>Tuples</dt>
<dd>
```erlang
{one, two, 3}
```
</dd>
</dl>
</section>

<section id="other-types" class="syntax">
<dl>
<dt>fun (function reference)</dt>
<dd>
```erlang
1> fun () -> ok end.
#Fun<erl_eval.20.80484245>
2> fun erlang:self/0.
#Fun<erlang.self.0>
```
</dd>
<dt>pid (process identifier)</dt>
<dd>
```erlang
1> self().
<0.35.0>
```
</dd>
<dt>port (port identifier, similar to pid)</dt>
<dd>
```erlang
1> erlang:open_port(
    {spawn, "/bin/ls"}, []).     
#Port<0.606>
```
</dd>
</dl>
</section>

<section id="not-types" class="syntax">
<dl>
<dt>booleans are atoms</dt>
<dd>
```erlang
true /= false
```
</dd>
<dt>characters are integers, strings are lists</dt>
<dd>
```erlang
"abc" == [$a, $b | [99]]
```
</dd>
<dt>records are tuples</dt>
<dd>
```erlang
-record(foo, {bar}).
#foo{bar=1} == {foo, 1}
```
</dd>
</dl>
</section>

<section id="variables" class="syntax">
<dl>
<dt>Variables are single assignment (and UpperCase)</dt>
<dd>
```erlang
1> X = 1, X = 2.
** exception error: no match …
```
</dd>
<dt>Assignment is pattern matching! (_ is wildcard)</dt>
<dd>
```erlang
1> {[X], _} = {[foo], bar}, X.
foo
2> {X, Y} = {foo, bar}, Y.
bar
```
</section>

<section id="guards" class="syntax">
<dl>
<dt>Functions and case can use guards when pattern matching</dt>
<dd>
```erlang
case date() of
  {2013, 10, N} when N =< 3 ->
     bob_is_in_vegas;
  {2013, 9, N} when N > 26 ->
     this_talk_already_happened;
  _ ->
     this_talk_hasnt_happened_yet
end.
```
</dd>
</section>

<section id="punctuation" class="syntax">
<dl>
<dt>Comma delimits expressions ("then")</dt>
<dd>
```erlang
first(), second(), third().
```
</dd>
<dt>Semicolon delimits clauses ("or")</dt>
```erlang
case X of
  1 -> one;
  2 -> two;
  _ -> other_number
end.
```
</dt>
<dt>Period ends the last clause (".")</dt>
<dd>
```erlang
simple_function() -> ok.
```
</dd>
</dl>
</section>

<section id="modules">
<h2>Modules</h2>
- Flat namespace
- Explicit export of public functions
- Referenced by name (atom)
- Compiled to ".beam" file
- Can be reloaded in a running system

```erlang
%% hello.erl
%% USAGE: hello:world().
-module(hello).
-export([world/0]).

world() -> io:format("hello world~n", []).
```
</section>

# More Info

* [Erlang.org](http://erlang.org)
* [Erlang-Central.org](http://erlang-central.org)
* [Erlang-Factory.com](http://erlang-factory.com)
* [LearnYouSomeErlang.com](http://LearnYouSomeErlang.com)
* \#erlang on IRC (freenode)

# Thanks!

+-------------+----------------------------------------------+
| **Slides**  | <http://bob.ippoli.to/intro-to-erlang-2013/> |
+-------------+----------------------------------------------+
| **Source**  | [github.com/etrepum/intro-to-erlang-2013]    |
+-------------+----------------------------------------------+
| **Email**   | bob@redivi.com                               |
+-------------+----------------------------------------------+
| **Twitter** | @etrepum                                     |
+-------------+----------------------------------------------+

[github.com/etrepum/intro-to-erlang-2013]: https://github.com/etrepum/intro-to-erlang-2013


