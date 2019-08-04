# Python基礎

## 基礎

- 文字列.upper()   
文字を大文字にする。  
`"Ola".upper() ➡ OLA`

- len(文字列)  
文字列の長さ取得  
`len("Ola") ➡　3`

---

## リスト

`[]`で定義。  
```python
lot = [3,4,5,6]
```

- len(リスト)  
要素数獲れる

- リスト.sort()  
リストの中身をソートする

- リスト.append(~~)  
~~をリストの最後の要素に追加する

- リスト.pop(要素番号)  
リストの要素番号の要素を削除

---

## 辞書(ディレクショナリ)

`{}`で定義する。  
```python
sikame = {'name':'Madoka','countory':'Mitakihara','numbers':[1,2,3]}  
```
これで3つのキーと値をもつ要素を作成した。  
次のように書くと書くキーの値を確認できる。
```python
print(sikame['name'])
>Madoka
```
リストと違って要素を引っ張てくるのにインデックスを覚えておく必要はないとかなんとか。

- 新しい要素を追加  
```python
sikame['facorite_language'] = `Python`
```

- 要素削除
```python
sikame.pop('numbers')
```

- 既存のキー変更  
```python
sikame['name'] = homura
```
---

## 条件文

```python
if 3 > 2:                 # 条件式
    print('Grate')        # 実行結果
else if 2 > 3:
    print('ettitititit')
else:
    print('Failed')
```

---

## 関数

とりあえず作ってみる
 ```pyhon
def hi():
    print('Hi tehere!')
    print('how are you?')

hi()
 ```

 引数付き
 ```python
def hi(name):
    print(name)

hi("Ola")
 ```

---

## ループ

```python
girls = ['mirai','emo','rinka']

for name in girls:
    print('Hi' + name)
```

指定した回数ループ
```python
for i in range(1,6)
    print(i)

# 実行結果
1
2
3
4
5
```