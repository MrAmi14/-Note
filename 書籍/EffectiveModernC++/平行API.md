# 平行API

## 二つの非同期に実行する方法

関数`doAsyncWork`を非同期で実装する方法は二つある。  

- スレッドベース方式  
`std::thread`を作成し、そのスレッド上で`doAsyncWork`を実行する方法。  
    ```c++
    int doAsyncWork();

    std::thread t(doAsyncWork)
    ```

- タスクベース方式  
`std::async`に`doAsyncWork`を渡す方法。  

    タスクベースプログラミングとは`std::async`を使用することではなく、以下の3つを土台とする設計方法である。  
    - 実行する仕事の単位（タスク）を定義する  
    - タスク間のデータの依存性と時間依存性を明確にする。  
    - 前項の依存性を維持しつつ、タスクを実行するように実行環境を構築、設定する。  

    ```c++
    auto future = std::async(doAsyncWork);
    ```  
    上例のようなコードでは、`std::async`へ渡した関数オブジェクト(doAsyncWork)を`タスク`と考える。

---

## スレッドが持つ3つの意味

c++平行ソフトウェアの`スレッド`が持つ、3つの意味をまとめる。  
- ハードウェアスレッド  
実際に演算を実行するスレッドである。現代のマシンアーキテクチャはCPUコア1つにつき、1つ以上のハードウェアスレッドを持つ。

- ソフトウェアスレッド  (OSスレッド、システムスレッド)  
オペレーティングシステムが全プロセスにわたり管理し、ハードウェアスレッド上で実行するようスケジューリングするスレッドである。  
ソフトウェアスレッドがブロックする場合(条件変数のウェイトなど)、他のブロックしていないスレッドを実行すればスループットを向上させられる。  
このため、通常はハードウェアスレッド数よりも多くのソフトウェアスレッドが作成できる。

- std::thread  
c++プロセス内で、下位に位置するソフトウェアスレッドのハンドルとしてふるまうオブジェクト

そもそも`スレッド`ってなによ？？　`プログラム処理の実行単位。ご飯を炊く。カレーを煮込む。関数`  
[ここみて](https://wa3.i-3-i.info/word12453.html)


`ソフトウェアスレッド`はハードウェアスレッドという共通のPCを扱う人たち。  
ソフスレマンは何にもいても、ハードウェアスレッドの数だけしか同時並行で作業できない。  
でも処理が止まったら他のソフスレマンに移行することはできるよ。

---

## スレッドベースよりもタスクベースのほうがいいってお話

スレッドベースだと、ソフトウェアスレッドの上限数に達しているのにも関わらず新たに作成する危険性があったり、  
`オーバーサブスクリプション`と呼ばれる問題に直面することもある(ハードウェアスレッドの数よりも実行待ちのソフトウェアスレッドの数のほうが多い状態)

これらの解決策として`タスクベース方式を使おう`です。  
なんでこっちだと解決するのかというと、タスクベースで関数を呼び出すと  
`新規ソフトウェアスレッドが作成されるとは限らない`のです。  

`std::async`はスケジューラ―が`doAsyncWork`の結果を必要とするスレッド上で、  
指定した関数を実行してもいいよってことを意味するんだって。  

---

## 2種類のstd::async

- std::launch::async  
必ず`非同期(別スレッド)`で実行される。  
    ``` c++
    foo () 
    {
          cout << "execute: " << __PRETTY_FUNCTION__ << endl;
          return time(NULL);
    }

     cout << "async foo" << endl;
     future<int> f1 = std::async(std::launch::async, foo);
     std::this_thread::sleep_for(std::chrono::milliseconds(10));
     cout << "after sleep" << endl;
     int r = f1.get();
     cout << r << endl;

    //実行結果
    async foo
    execute: int foo()
    after sleep
    1388332617
    ```

    この場合、`future<int> f1 = std::async(std::launch::async, foo);`の時点で、非同期で  
    `foo()`の処理が呼ばれている。


-  std::launch::deferred  
std::asyncが返したfutureに対し、`get`もしくは`wait`を呼び出した場合のみfを実行可能とする。  
すなわち`f`の実行はｋれらが呼び出されるまで`遅延`させられ、`get`,`wait`を実行したときに`同時期に実行される`  
この場合、呼び出し側は`f`の実行が完了するまでブロックする。`get`も`wait`も実行しなきゃ`ｆ`は実行しない。

    ``` c++
    foo () 
    {
          cout << "execute: " << __PRETTY_FUNCTION__ << endl;
          return time(NULL);
    }

    cout << "async deferred foo" << endl;
    future<int> f1 = std::async(std::launch::deferred, foo);
    std::this_thread::sleep_for(std::chrono::milliseconds(10));
    cout << "after sleep" << endl;
    int r = f1.get();
    cout << r << endl;

    //実行結果
    async foo
    after sleep
    execute: int foo()
    1388332617
    ```

    この場合、`f1.get();`が呼ばれるまで、 `foo()`の処理は呼ばれまてんろうしさま。


