.. Serialization and Deserialization

シリアライズと逆シリアライズ
=================================

.. Serialization is the act of converting application data into a
.. form rendering.  Deserialization is the act of converting data
.. resulting from a form submission into application data.

シリアライズは、アプリケーションデータをフォームレンダリングに変換する
行為です。逆シリアライズは、フォーム送信に起因するデータをアプリケーション
データに変換する行為です。


.. Serialization

シリアライズ
-------------

.. Serialization is what happens when you ask Deform to render a form
.. given a :term:`schema`.  Here's a high-level overview of what happens
.. when you ask Deform to do this:

シリアライズとは、 Deform に :term:`schema` を与えてフォームをレンダリング
するように伝えたときに行われることです。その場合に起こることの高レベルの
概略は以下の通りです:


.. - For each :term:`schema node` in the :term:`schema` provided by the
..   application developer, Deform creates a :term:`field`.  This happens
..   recursively for each node in the schema.  As a result, a tree of
..   fields is created, mirroring the nodes in the schema.

- アプリケーション開発者が提供した :term:`schema` の中のそれぞれの
  :term:`schema node` について、 Deform が :term:`field` を生成します。
  これは、スキーマ中の各ノードに対して再帰的に起こります。その結果、
  スキーマ中のノードを反映してフィールドの木構造が生成されます。


.. - Each field object created as a result of the prior step knows about
..   its associated schema node (it has a ``field.schema`` attribute);
..   each field also knows about an associated :term:`widget` object (it
..   has a ``field.widget`` attribute).  This widget object may be a
..   default widget based on the schema node type or it might be
..   overridden by the application developer for a particular rendering.

- 前のステップの結果生成されたそれぞれのフィールド・オブジェクトは、
  その関連するスキーマノードのことを知っています(``field.schema`` 属性
  を持っています); それぞれのフィールドはさらに、関連する
  :term:`widget` オブジェクトのことを知っています (``field.widget`` 属性
  を持っています)。このウィジェット・オブジェクトは、スキーマノード型に
  基づいたデフォルトウィジェットかもしれません。あるいは、アプリケーション
  開発者によって特定のレンダリングのためにオーバーライドされるかもしれません。


.. - Deform passes an :term:`appstruct` to the root schema node's
..   ``serialize`` method to obtain a :term:`cstruct`.  The root schema
..   node is responsible for consulting its children nodes during this
..   process to serialilize the entirety of the data into a
..   single :term:`cstruct`.

- Deform は :term:`cstruct` を得るために :term:`appstruct` を root
  スキーマノードの ``serialize`` メソッドへ渡します。
  root スキーマノードは、データ全体を単一の :term:`cstruct` の中に
  シリアライズするために、このプロセス中で子ノードを調べることに責任を
  持ちます。


.. - Deform passes the resulting :term:`cstruct` to the root widget
..   object's ``serialize`` method to generate an HTML form rendering.
..   The root widget object is responsible for consulting its children
..   nodes during this process to serialilize the entirety of the data
..   into an HTML form.

- HTML フォームレンダリングを生成するために、 Deform は得られた
  :term:`cstruct` を root ウィジェット・オブジェクトの ``serialize``
  メソッドへ渡します。 root ウィジェット・オブジェクトは、データ全体を
  HTML フォームにシリアライズするためにこのプロセスの間子ノードを調べる
  ことに責任を持ちます。


.. If you were to attempt to produce a high-level overview diagram this
.. process, it might look like this:

このプロセスの高レベルの概略図を作ろうとしたら、このようになるでしょう:


.. code-block:: text

   appstruct -> cstruct -> form
              |          |
              v          v
           schema      widget


.. Peppercorn Structure Markers

Peppercorn 構造マーカー
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. You'll see the default deform widget "serializations" (form
.. renderings) make use of :term:`Peppercorn` *structure markers*.

デフォルトの deform ウィジェットの「シリアライズ」(フォームレンダリング) が
:term:`Peppercorn` *構造マーカー* を使用することがわかるでしょう。


.. Peppercorn is a library that is used by Deform; it allows Deform to
.. treat the :term:`form controls` in an HTML form submission as a
.. *stream* instead of a flat mapping of name to value.  To do so, it
.. uses hidden form elements to denote structure.

Peppercorn は Deform によって使用されるライブラリです; それは Deform が
HTML フォーム送信中の :term:`form controls` を名前から値へのフラットな
マッピングとして扱う代わりに *ストリーム* として扱えるようにします。
これを実現するため、 Peppercorn は構造を表示するために hidden フォーム
要素を使用します。


.. Peppercorn structure markers come in pairs which have a begin token
.. and an end token.  For example, a given form rendering might have a
.. part that looks like so:

Peppercorn 構造マーカーは、開始トークンと終了トークンのペアとして現れます。
例えば、与えられたフォームレンダリングの中に以下のような部分があるかも
しれません:


.. code-block:: xml
   :linenos:

     <html>
      ...
       <input type="hidden" name="__start__" value="date:mapping"/>
       <input name="day"/>
       <input name="month"/>
       <input name="year"/>
       <input type="hidden" name="__end__"/>
      ...
     </html>

  
.. The above example shows an example of a pair of peppercorn structure
.. markers which begin and end a *mapping*.  The example uses this pair
.. to means that a the widget related to the *date* node in the schema
.. will be be passed a :term:`pstruct` that is a dictionary with multiple
.. values during deserialization: the dictionary will include the keys
.. ``day`` , ``month``, and ``year``, and the values will be the values
.. provided by the person interacting with the related form controls.

上記の例は、 *mapping* の開始と終了の一組の peppercorn 構造マーカーの
例を示しています。この例では、逆シリアライズの間にスキーマ中の *date*
ノードと関係するウィジェットに対して複数の値を持った辞書である
:term:`pstruct` が渡されるための手段としてそのペアが使用されます:
辞書にはキー *day*, *month*, *year* が含まれているでしょう。
また、その値は関連するフォームコントロールとやり取りした人によって
提供された値になるでしょう。


.. Other uses of Peppercorn structure markers include: a "confirm
.. password" widget can render a peppercorn mapping with two text inputs
.. in it, a "mapping widget" can serve as a substructure for a fieldset.
.. Basically, Peppercorn makes it more pleasant to deal with form
.. submission data by pre-converting the data from a flat mapping into a
.. set of mappings, sequences, and strings during deserialization.

Peppercorn 構造マーカーの他の用途には次のものが含まれます: 「パスワード
確認」ウィジェットは、その中の2つのテキスト入力を持つ peppercorn マッピングを
レンダリングできます。「マッピングウィジェット」は fieldset の下部構造として
使うことができます。基本的に、 Peppercorn は逆シリアライズ中にデータを
フラットなマッピングからマッピング、シーケンスおよび文字列の集合に事前
変換することにより、フォーム送信データを扱いやすくします。


.. However, if a widget doesn't want to do anything fancy and a particular
.. widget is completely equivalent to one form control, it doesn't need
.. to use any Peppercorn structure markers in its rendering.

しかし、ウィジェットが何か手の込んだことをしようとせず、特定の
ウィジェットが単一のフォームコントロールと完全に等価な場合、
そのレンダリング中に Peppercorn 構造マーカーを使用する必要は
まったくありません。


.. .. note:: See the `Peppercorn documentation
..    <http://docs.pylonsproject.org/projects/peppercorn/dev/>`_ for more
..    information about using peppercorn structure markers in HTML.

.. note::

  HTML 中で peppercorn 構造マーカーを使用することについての詳細は、
  `Peppercorn ドキュメンテーション
  <http://docs.pylonsproject.org/projects/peppercorn/dev/>`_
  を見てください。

 
.. Deserialization

逆シリアライズ
---------------

.. High-level overview of how "deserialization" (converting form control
.. data resulting from a form submission to application data) works:

「逆シリアライズ」 (フォーム送信の結果得られたフォームコントロール
データをアプリケーションデータに変換すること) が、どのように働くかの
高レベルの概略:


.. - For each :term:`schema node` in the :term:`schema` provided by the
..   application developer, Deform creates a :term:`field`.  This happens
..   recursively for each node in the schema.  As a result, a tree of
..   fields is created, mirroring the nodes in the schema.

- アプリケーション開発者が提供した :term:`schema` の中の各
  :term:`schema node` について、 Deform が :term:`field` を作成します。
  これはスキーマ内のそれぞれのノードに対して再帰的に起こります。その結果、
  スキーマ内のノードを反映してフィールドの木構造が作成されます。


.. - Each field object created as a result of the prior step knows about
..   its associated schema node (it has a ``field.schema`` attribute);
..   each field also knows about an associated :term:`widget` object (it
..   has a ``field.widget`` attribute).  This widget object may be a
..   default widget based on the schema node type or it might be
..   overridden by the application developer for a particular rendering.

- 前のステップの結果として生成されたそれぞれのフィールド・オブジェクトは、
  関連するスキーマノードのことを知っています (``field.schema`` 属性を
  持っています); また、それぞれのフィールドは関連する :term:`widget`
  オブジェクトのことを知っています (``field.widget`` 属性を持っています) 。
  このウィジェット・オブジェクトは、スキーマノード型に基づいたデフォルト
  ウィジェットかもしれません。あるいは、アプリケーション開発者によって
  特定のレンダリングのためにオーバーライドされるかもしれません。


.. - Deform passes a set of :term:`form controls` to the ``parse`` method
..   of :term:`Peppercorn` in order to obtain a :term:`pstruct`.

- Deform は :term:`pstruct` を得るために :term:`form controls` のセットを
  :term:`Peppercorn` の ``parse`` メソッドに渡します。


.. - Deform passes the resulting :term:`pstruct` to the root widget
..   node's ``deserialize`` method in order to generate a
..   :term:`cstruct`.

- Deform は :term:`cstruct` を生成するために、得られた
  :term:`pstruct` を root ウィジェットノードの ``deserialize`` メソッドに
  渡します。


.. - Deform passes the resulting :term:`cstruct` to the root schema
..   node's ``deserialize`` method to generate an :term:`appstruct`.
..   This may result in a validation error.  If a validation error
..   occurs, the form may be rerendered with error markers in place.

- Deform は :term:`appstruct` を生成するために、得られた
  :term:`cstruct` を root スキーマノードの ``deserialize`` メソッドに
  渡します。これはバリデーションエラーを生じるかもしれません。
  バリデーションエラーが起きた場合、適当な場所にエラーマーカーが挿入
  されたフォームがレンダリングされます。


.. If you were to attempt to produce a high-level overview diagram this
.. process, it might look like this:

このプロセスの高レベルの概略図を作ろうとしたら、このようになるでしょう:


.. code-block:: text

   formcontrols -> pstruct -> cstruct -> appstruct
                |          |          |
                v          v          v
            peppercorn   widget    schema


.. When a user presses the submit button on any Deform form, Deform
.. itself runs the resulting :term:`form controls` through the
.. ``peppercorn.parse`` method.  This converts the form data into a
.. mapping.  The *structure markers* in the form data indicate the
.. internal structure of the mapping.

ユーザが任意の Deform フォーム上で submit ボタンを押した場合、 Deform
それ自身は結果として生じる :term:`form controls` を
``peppercorn.parse`` メソッドに渡します。これはフォームデータをマッピングに
変換します。フォームデータ中の *構造マーカー* は、マッピングの内部構造
を示します。


.. For example, if the form submitted had the following data:

例えば、送信されたフォームが次のデータを持っている場合:


.. code-block:: xml
   :linenos:

     <html>
      ...
       <input type="hidden" name="__start__" value="date:mapping"/>
       <input name="day"/>
       <input name="month"/>
       <input name="year"/>
       <input type="hidden" name="__end__"/>
      ...
     </html>


.. There would be a ``date`` key in the root of the pstruct mapping which
.. held three keys: ``day``, ``month``, and ``year``.

pstruct マッピングの root に ``date`` キーがあり、 ``day``, ``month``,
``year`` の 3つのキーが含まれているでしょう:


.. .. note:: See the `Peppercorn documentation
..    <http://docs.pylonsproject.org/projects/peppercorn/dev/>`_ for more
..    information about the result of the ``peppercorn.parse`` method and how it
..    relates to form control data.

.. note::

  ``peppercorn.parse`` メソッドの結果と、それがフォームコントロールデータと
  どのように関係するかについての詳細は、 `Peppercorn ドキュメンテーション
  <http://docs.pylonsproject.org/projects/peppercorn/dev/>`_ を参照して
  ください。


.. The bits of code that are "closest" to the browser are called
.. "widgets".  A chapter about creating widgets exists in this
.. documentation at :ref:`writing_a_widget`.

ブラウザの最も「近くにある」コード片は「ウィジェット」と呼ばれます。
ウィジェットの作成に関する章はこのドキュメンテーションの
:ref:`writing_a_widget` に存在しています。


.. A widget has a ``deserialize`` method.  The deserialize method is
.. passed a structure (a :term:`pstruct`) which is shorthand for
.. "Peppercorn structure".  A :term:`pstruct` might be a string, it might
.. be a mapping, or it might be a sequence, depending on the output of
.. ``peppercorn.parse`` related to its schema node against the form
.. control data.

ウィジェットは ``deserialize`` メソッドを持っています。 deserialize
メソッドには、ある構造 (:term:`pstruct`) が渡されます。これは
「 *Peppercorn* 構造」の省略形です。 :term:`pstruct` は文字列か、
マッピングか、シーケンスかもしれません。それはフォームコントロールデータ
に対するスキーマノードと関係する ``peppercorn.parse`` の出力に依存します。


.. The job of the deserialize method of a widget is to convert the
.. pstruct it receives into a :term:`cstruct`.  A :term:`cstruct` is a
.. shorthand for "Colander structure".  It is often a string, a mapping
.. or a sequence.

ウィジェットの deserialize メソッドの仕事は、受け取った pstruct を
:term:`cstruct` に変換することです。 :term:`cstruct` は「 *Colander* 構造」
の省略形です。多くの場合それは文字列、マッピングあるいはシーケンスです。


.. An application eventually wants to deal in types less primitive than
.. strings: a model instance or a datetime object.  An :term:`appstruct`
.. is the data that an application that uses Deform eventually wants to
.. deal in.  Therefore, once a widget has turned a :term:`pstruct` into a
.. :term:`cstruct`, the :term:`schema node` related to that widget is
.. responsible for converting that cstruct to an :term:`appstruct`.  A
.. schema node possesses its very own ``deserialize`` method, which is
.. responsible for accepting a :term:`cstruct` and returning an
.. :term:`appstruct`.

アプリケーションは、いつかはモデルインスタンスや datetime オブジェクト
のような文字列ほど原始的でない型を扱いたいと思うでしょう。
:term:`appstruct` は、 Deform を使用するアプリケーションが最終的に扱いたい
データです。したがって、ウィジェットが一旦 :term:`pstruct` を
:term:`cstruct` に変換したら、そのウィジェットに関連付けられた
:term:`schema node` が cstruct を :term:`appstruct` に変換することに責任を
持ちます。スキーマノードはそれ自身の ``deserialize`` メソッドを持っています。
それは :term:`cstruct` を受け取って :term:`appstruct` を返すことに責任を
持ちます。


.. Raising Errors During Deserialization

逆シリアライズ中にエラーを送出する
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. If a widget determines that a pstruct value cannot be converted
.. successfully to a cstruct value during deserialization, it may raise
.. an :exc:`colander.Invalid` exception.

ウィジェットが逆シリアライズ 中に pstruct 値を cstruct 値に正常に変換
できないと判断した場合、 :exc:`colander.Invalid` 例外が送出されます。


.. When it raises this exception, it can use the field object as a
.. "scratchpad" to hold on to other data, but it must pass a ``value``
.. attribute to the exception constructor.  For example:

それが例外を送出する場合、フィールド・オブジェクトを他のデータを保持
するための「メモ帳」として使うことができますが、それは ``value`` 属性を
例外コンストラクタへ渡さなければなりません。例えば:


.. code-block:: python
   :linenos:

    import colander

    def serialize(self, field, cstruct, readonly=False):
        if cstruct is colander.null:
            cstruct = ''
        confirm = getattr(field, 'confirm', '')
        template = readonly and self.readonly_template or self.template
        return field.renderer(template, field=field, cstruct=cstruct,
                              confirm=confirm, subject=self.subject,
                              confirm_subject=self.confirm_subject,
                              )

    def deserialize(self, field, pstruct):
        if pstruct is colander.null:
            return colander.null
        value = pstruct.get('value') or ''
        confirm = pstruct.get('confirm') or ''
        field.confirm = confirm
        if value != confirm:
            raise Invalid(field.schema, self.mismatch_message, value)
        return value


.. The schema type associated with this widget is expecting a single
.. string as its cstruct.  The ``value`` passed to the exception
.. constructor raised during the ``deserialize`` when ``value !=
.. confirm`` is used as that ``cstruct`` value when the form is
.. rerendered with error markers.  The ``confirm`` value is picked off
.. the field value when the form is rerendered at this time.

このウィジェットに関連したスキーマ型は、その cstruct として単一の文字列
を期待しています。逆シリアライズ中に ``value != confirm`` の場合、例外
コンストラクタに渡された ``value`` はフォームがエラーマーカー付きで
再レンダリングされるときに ``cstruct`` 値として使用されます。フォームが
再レンダリングされるときにフィールド値から ``confirm`` 値が取られます。


Say What?
---------

.. Q: "So deform colander and peppercorn are pretty intertwingled?"

.. A: "Colander and Peppercorn are unrelated; Deform is effectively
..     something that integrates colander and peppercorn together."


Q: 「要するに deform colandar peppercorn はかなり絡み合っている
(intertwingle) ってこと?」

A: 「 Colander と Peppercorn は無関係です; Deform は事実上 colander と
peppercorn を統合するものです」
