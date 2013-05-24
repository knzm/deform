.. Common Needs

よくあるニーズ
==============

.. This chapter collects solutions for requirements that will often crop
.. up once you start using Deform for real world applications.

この章では、 Deform を実世界のアプリケーションで使用したときに
しばしば必要となる要件の解決策を収集します。


.. Changing the Default Widget Associated With a Field

.. _changing_a_default_widget:

フィールドに関連付けられたデフォルトウィジェットの変更
------------------------------------------------------

.. Let's take another look at our familiar schema:

おなじみのスキーマを改めて見てみましょう:


.. code-block:: python
   :linenos:

   import colander

   class Person(colander.MappingSchema):
       name = colander.SchemaNode(colander.String())
       age = colander.SchemaNode(colander.Integer(),
                                 validator=colander.Range(0, 200))

   class People(colander.SequenceSchema):
       person = Person()

   class Schema(colander.MappingSchema):
       people = People()

   schema = Schema()


.. This schema renders as a *sequence* of *mapping* objects.  Each
.. mapping has two leaf nodes in it: a *string* and an *integer*.  If you
.. play around with the demo at
.. `http://deformdemo.repoze.org/sequence_of_mappings/
.. <http://deformdemo.repoze.org/sequence_of_mappings/>`_ you'll notice
.. that, although we don't actually specify a particular kind of widget
.. for each of these fields, a sensible default widget is used.  This is
.. true of each of the default types in :term:`Colander`.  Here is how
.. they are mapped by default.  In the following list, the schema type
.. which is the header uses the widget underneath it by default.

このスキーマは *mapping* オブジェクトの *sequence* としてレンダリングします。
それぞれの mapping は *string* と *integer* の 2つの葉節点を持っています。
`http://deformdemo.repoze.org/sequence_of_mappings/
<http://deformdemo.repoze.org/sequence_of_mappings/>`_
にあるデモを試せば、それぞれのフィールドに対して実際には特定の種類の
ウィジェットを指定していなくても、それに相応しいデフォルトのウィジェットが
使用されることに気がつくでしょう。これは :term:`Colander` のそれぞれの
デフォルト型に該当します。デフォルトでそれがどのようにマッピングされるかを
以下に示します。以下のリストの中で、見出しのスキーマ型はその直下の
ウィジェットをデフォルトで使用します。


:class:`colander.Mapping`
   :class:`deform.widget.MappingWidget`

:class:`colander.Sequence`
    :class:`deform.widget.SequenceWidget`

:class:`colander.String`
    :class:`deform.widget.TextInputWidget`

:class:`colander.Integer`
    :class:`deform.widget.TextInputWidget`

:class:`colander.Float`
    :class:`deform.widget.TextInputWidget`

:class:`colander.Decimal`
    :class:`deform.widget.TextInputWidget`

:class:`colander.Boolean`
    :class:`deform.widget.CheckboxWidget`

:class:`colander.Date`
    :class:`deform.widget.DateInputWidget`

:class:`colander.DateTime`
    :class:`deform.widget.DateTimeInputWidget`

:class:`colander.Tuple`
    :class:`deform.widget.Widget`

.. note::

   .. Not just any widget can be used with any schema type; the
   .. documentation for each widget usually indicates what type it can be
   .. used against successfully.  If all existing widgets provided by
   .. Deform are insufficient, you can use a custom widget.  See
   .. :ref:`writing_a_widget` for more information about writing 
   .. a custom widget.

   すべてのウィジェットが任意のスキーマ型と共に使用できるわけではありません;
   各ウィジェットのドキュメンテーションには通常、そのウィジェットに対して
   どんな型が使用できるかが示されています。 Deform が提供する既存の
   ウィジェットの中に適当なものがなければ、カスタムウィジェットを使用
   することができます。カスタムウィジェットを書くことについて、詳細は
   :ref:`writing_a_widget` を参照してください。


.. If you are creating a schema that contains a type which is not in this
.. list, or if you'd like to use a different widget for a particular
.. field, or you want to change the settings of the default widget
.. associated with the type, you need to associate the field with the
.. widget "by hand".  There are a number of ways to do so, as outlined in
.. the sections below.

このリストに載っていない型を含むスキーマを作成している場合や、
特定のフィールドに対して異なるウィジェットを使用したい場合、あるいは
型に関連付けられたデフォルトウィジェットの設定を変更したい場合、
「手作業で」ウィジェットにフィールドを関連付ける必要があります。
以下の節で概説されるように、それには多くの方法があります。


.. As an argument to a :class:`colander.SchemaNode` constructor

:class:`colander.SchemaNode` コンストラクタに対する引数として
+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

.. As of Deform 0.8, you may specify the widget as part of the
.. schema:

Deform 0.8 から、スキーマの一部としてウィジェットを指定することが
できるようになりました:


.. code-block:: python
   :linenos:

   import colander

   from deform import Form
   from deform.widget import TextInputWidget

   class Person(colander.MappingSchema):
       name = colander.SchemaNode(colander.String(),
                                  widget=TextAreaWidget())
       age = colander.SchemaNode(colander.Integer(),
                                 validator=colander.Range(0, 200))

   class People(colander.SequenceSchema):
       person = Person()

   class Schema(colander.MappingSchema):
       people = People()

   schema = Schema()

   myform = Form(schema, buttons=('submit',))


.. Note above that we passed a ``widget`` argument to the ``name`` schema
.. node in the ``Person`` class above.  When a schema containing a node
.. with a ``widget`` argument to a schema node is rendered by Deform, the
.. widget specified in the node constructor is used as the widget which
.. should be associated with that node's form rendering.  In this case,
.. we'll be using a :class:`deform.widget.TextAreaWidget` as the ``name``
.. widget.

上記の例で ``Person`` クラスの ``name`` スキーマノードに ``widget`` 引数を
渡していることに注意してください。スキーマが ``widget`` 引数を持つ
スキーマノードを含む場合、 Deform によってスキーマがレンダリングされる際に
ノードのコンストラクタで指定されたウィジェットが、そのノードのフォーム
レンダリングに関連付けられたウィジェットとして使用されます。この場合、
``name`` ウィジェットとして :class:`deform.widget.TextAreaWidget` が
使用されます。


.. note::

  .. Widget associations done in a schema are always overridden by
  .. explicit widget assigments performed via
  .. :meth:`deform.Field.__setitem__` and
  .. :meth:`deform.Field.set_widgets`.

  スキーマの中で行われたウィジェットの関連付けは、
  :meth:`deform.Field.__setitem__` と :meth:`deform.Field.set_widgets`
  による明示的なウィジェットの割り当てによっていつでもオーバーライド
  されます。


.. Using dictionary access to change the widget

ウィジェットを変更するために辞書アクセスを用いる
++++++++++++++++++++++++++++++++++++++++++++++++

.. After the :class:`deform.Form` constructor is called with the schema
.. you can change the widget used for a particular field by using
.. dictionary access to get to the field in question.  A
.. :class:`deform.Form` is just another kind of :class:`deform.Field`, so
.. the method works for either kind of object.  For example:

:class:`deform.Form` コンストラクタがスキーマとともに呼び出された後で
特定のフィールドに対して使用されるウィジェットを変更することができます。
それには、問題となるフィールドに到達する辞書アクセスを用います。
:class:`deform.Form` は単に :class:`deform.Field` の異なる種類です。
したがって、そのメソッドはどちらの種類のオブジェクトに対しても使えます。
例えば:


.. code-block:: python
   :linenos:

   from deform import Form
   from deform.widget import TextInputWidget

   myform = Form(schema, buttons=('submit',))
   myform['people']['person']['name'].widget = TextInputWidget(size=10)


.. This associates the :class:`~colander.String` field named ``name``
.. in the rendered form with an explicitly created
.. :class:`deform.widget.TextInputWidget` by finding the ``name`` field
.. via a series of ``__getitem__`` calls through the field
.. structure, then by assigning an explicit ``widget`` attribute to the
.. ``name`` field.

これは、レンダリングされたフォームの中で ``name`` という名前の
:class:`~colander.String` フィールドを、明示的に生成された
:class:`~deform.widget.TextInputWidget` と関連付けます。
関連付けは、フィールドの構造にしたがって一連の ``__getitem__`` 呼び出しを
行って ``name`` フィールドを見つけ、その後 ``name`` フィールドに明示的な
``widget`` 属性を設定することで行われます。


.. You might want to do this in order to pass a ``size``
.. argument to the explicit widget creation, indicating that the size of
.. the ``name`` input field should be 10em rather than the default.  

これによって、明示的なウィジェット生成に ``size`` 引数を渡して
``name`` 入力フィールドのサイズをデフォルトではなく 10em になるように
することができます。


.. Although in the example above, we associated the ``name`` field with
.. the same type of widget as its default we could have just as easily
.. associated the ``name`` field with a completely different widget using
.. the same pattern.  For example:

上記の例において ``name`` フィールドをデフォルトと同じ型のウィジェットに
関連付けましたが、同じパターンを使用して、簡単に ``name`` フィールドを
まったく異なるウィジェットと関連付けることができます。例えば:


.. code-block:: python
   :linenos:

   from deform import Form
   from deform.widget import TextInputWidget

   myform = Form(schema, buttons=('submit',))
   myform['people']['person']['name'].widget = TextAreaWidget()


.. The above renders an HTML ``textarea`` input element for the ``name``
.. field instead of an ``input type=text`` field.  This probably doesn't
.. make much sense for a field called ``name`` (names aren't usually
.. multiline paragraphs); but it does let us demonstrate how different
.. widgets can be used for the same field.

上記は ``name`` フィールドに対して ``input type=text`` フィールドの
代わりに HTML ``textarea`` 入力要素をレンダリングします。これは、おそらく
``name`` という名前のフィールドに対してはあまり意味がないでしょう
(名前は通常は複数行の段落ではありません); しかし、それは、どのようにして
異なるウィジェットを同じフィールドに対して使用することができるかの具体例を
示します。


.. Using the :meth:`deform.Field.set_widgets` method

:meth:`deform.Field.set_widgets` メソッドを用いる
+++++++++++++++++++++++++++++++++++++++++++++++++

.. Equivalently, you can also use the :meth:`deform.Field.set_widgets`
.. method to associate multiple widgets with multiple fields in a form.
.. For example:

同様に、フォームの複数のフィールドに複数のウィジェットを関連付けるために
:meth:`deform.Field.set_widgets` メソッドを使用することができます。
例えば:


.. code-block:: python
   :linenos:

   from deform import Form
   from deform.widget import TextInputWidget

   myform = Form(schema, buttons=('submit',))
   myform.set_widgets({'people.person.name':TextAreaWidget(),
                       'people.person.age':TextAreaWidget()})


.. Each key in the dictionary passed to :meth:`deform.Field.set_widgets`
.. is a "dotted name" which resolves to a single field element.  Each
.. value in the dictionary is a widget instance.  See
.. :meth:`deform.Field.set_widgets` for more information about this
.. method and dotted name resolution, including special cases which
.. involve the "splat" (``*``) character and the empty string as a key
.. name.

:meth:`deform.Field.set_widgets` に渡された辞書のそれぞれのキーは、
単一のフィールド要素に解決される "dotted name" です。辞書のそれぞれの値は
ウィジェットのインスタンスです。キー名として "アスタリスク" (``*``) 文字と
空文字列が関係する特殊なケースを含めて、このメソッドと dotted name の解決に
関する詳細は :meth:`deform.Field.set_widgets` を参照してください。


.. Using Text Input Masks

.. _masked_input:

テキスト入力マスクの使用
------------------------

.. The :class:`deform.widget.TextInputWidget` and
.. :class:`deform.widget.CheckedInputWidget` widgets allow for the use of
.. a fixed-length text input mask.  Use of a text input mask causes
.. placeholder text to be placed in the text field input, and restricts
.. the type and length of the characters input into the text field.

:class:`deform.widget.TextInputWidget` と
:class:`deform.widget.CheckedInputWidget` ウィジェットでは、固定長の
テキスト入力マスクが使えます。テキスト入力マスクを使用することによって、
テキストフィールド入力にプレースホルダーテキストが置かれ、テキスト
フィールドに入力される文字の種類と長さが制限されます。


.. For example:

例えば:


.. code-block: python

   form['ssn'].widget = TextInputWidget(mask='999-99-9999')


.. When using a text input mask:

テキスト入力マスクを使う場合:


.. ``a`` represents an alpha character (A-Z,a-z)

.. ``9`` represents a numeric character (0-9)

.. ``*`` represents an alphanumeric character (A-Z,a-z,0-9)

``a`` はアルファベット文字を表します (A-Z,a-z)

``9`` は数字を表します (0-9)

``*`` は英数字を表します (A-Z,a-z,0-9)


.. All other characters in the mask will be considered mask literals.

マスクに含まれる他のすべての文字は、マスクリテラルとみなされます。


.. By default the placeholder text for non-literal characters in the
.. field will be ``_`` (the underscore character).  To change this for a
.. given input field, use the ``mask_placeholder`` argument to the
.. TextInputWidget:

デフォルトで、フィールド内の非リテラル文字のためのプレースホルダー
テキストは ``_`` (アンダースコア文字) になります。与えられた入力フィールド
に対してこれを変更するには、 TextInputWidget の ``mask_placeholder`` 引数
を使用してください:


.. code-block:: python

   form['date'].widget = TextInputWidget(mask='99/99/9999', 
                                         mask_placeholder="-")


.. Example masks:

マスクの例:


.. Date
..     99/99/9999

.. US Phone
..     (999) 999-9999

.. US SSN
..     999-99-9999

日付
    99/99/9999

アメリカの電話番号
    (999) 999-9999

アメリカの社会保障番号 (SSN)
    999-99-9999


.. When this option is used, the :term:`jquery.maskedinput` library must
.. be loaded into the page serving the form for the mask argument to have
.. any effect.  A copy of this library is available in the
.. ``static/scripts`` directory of the :mod:`deform` package itself.

このオプションが使用される場合、マスク引数が効果を持つために、フォームを
返すページに :term:`jquery.maskedinput` ライブラリをロードしなければ
なりません。このライブラリのコピーは :mod:`deform` パッケージ自体の
``static/scripts`` ディレクトリにあります。


.. See `http://deformdemo.repoze.org/text_input_masks/
.. <http://deformdemo.repoze.org/text_input_masks/>`_ for a working
.. example.

`http://deformdemo.repoze.org/text_input_masks/
<http://deformdemo.repoze.org/text_input_masks/>`_
で動作する例を見てください。


.. Use of a text input mask is not a replacement for server-side
.. validation of the field; it is purely a UI affordance.  If the data
.. must be checked at input time a separate :term:`validator` should be
.. attached to the related schema node.

テキスト入力マスクを使用することは、フィールドに対するサーバーサイドの
バリデーションの代わりにはなりません; それは純粋に UI アフォーダンスです。
入力時にデータをチェックしなければならない場合、関連するスキーマノードに
:term:`validator` を取り付ける必要があります。


.. Using the AutocompleteInputWidget

.. _autocomplete_input:

AutocompleteInputWidget の使用
---------------------------------

.. The :class:`deform.widget.AutocompleteInputWidget` widget allows for
.. client side autocompletion from provided choices in a text input
.. field. To use this you **MUST** ensure that :term:`jQuery` and the
.. :term:`JQuery UI` plugin are available to the page where the
.. :class:`deform.widget.AutocompleteInputWidget` widget is rendered.

:class:`deform.widget.AutocompleteInputWidget` ウィジェットは、提供された
選択肢からのクライアントサイドでのテキスト入力フィールドの自動補完を
可能にします。 :class:`deform.widget.AutocompleteInputWidget` ウィジェット
を利用するためには、 **必ず** このウィジェットがレンダリングされるページで
:term:`jQuery` と :term:`jQuery UI` プラグインを有効にしなければなりません。


.. For convenience a version of the :term:`JQuery UI` (which includes the
.. ``autocomplete`` sublibrary) is included in the :mod:`deform` static
.. directory. Additionally, the :term:`JQuery UI` styles for the
.. selection box are also included in the :mod:`deform` ``static``
.. directory. See :ref:`serving_up_the_rendered_form` and
.. :ref:`get_widget_resources` for more information about using the 
.. included libraries from your application.

便宜上 jQuery UI のバージョン (``autocomplete`` サブライブラリを含む) が
:mod:`deform` の静的ディレクトリに含まれています。さらに、セレクション
ボックスのための jQuery UI スタイルも :mod:`deform` ``static`` ディレクトリ
に含まれています。インクルードされたライブラリをアプリケーションから
利用することについての詳細は、 :ref:`serving_up_the_rendered_form` と
:ref:`get_widget_resources` を参照してください。


.. A very simple example of using
.. :class:`deform.widget.AutocompleteInputWidget` follows:

:class:`deform.widget.AutocompleteInputWidget` を使う非常に簡単な例は
次の通りです:


.. code-block:: python

   form['frozznobs'].widget = AutocompleteInputWidget(
                                values=['spam', 'eggs', 'bar', 'baz'])


.. Instead of a list of values a URL can be provided to values:

values にはリストの代わりに URL を渡すことができます:


.. code-block:: python

   form['frobsnozz'].widget = AutocompleteInputWidget(
                                values='http://example.com/someapi')


.. In the above case a call to the url should provide results in a JSON-compatible
.. format or JSONP-compatible response if on a different host than the
.. application.  Something like either of these structures in JSON are suitable:

上記の例で url がアプリケーションとは異なるホスト上にある場合、その
呼び出しは JSON 互換フォーマットあるいは JSONP 互換のレスポンスで結果を
返す必要があります。これらの構造のいずれかのようなものは JSON 形式として
適切です:


::

    //Items are used as both value and label
    ['item-one', 'item-two', 'item-three']

    //Separate values and labels
    [
        {'value': 'item-one', 'label': 'Item One'},
        {'value': 'item-two', 'label': 'Item Two'},
        {'value': 'item-three', 'label': 'Item Three'}
    ]


.. The autocomplete plugin will add a query string to the request URL with the
.. variable ``term`` which contains the user's input at that momement.  The server
.. may use this to filter the returned results.  

autocomplete プラグインは、リクエスト URL に対するクエリ文字列に変数
``term`` を追加します。それはその時点でのユーザの入力を含んでいます。
サーバは、返される結果をフィルターするためにこれを使用することができます。


.. For more information, see http://api.jqueryui.com/autocomplete/#option-source
.. - specifically, the section concerning the ``String`` type for the ``source``
.. option.

詳細は、 http://api.jqueryui.com/autocomplete/#option-source - 特に
``source`` オプションに対する ``String`` 型に関する節を参照してください。


.. Some options for the :term:`jquery.autocomplete` plugin are mapped and
.. can be passed to the widget. See
.. :class:`deform.widget.AutocompleteInputWidget` for details regarding the
.. available options. Passing options looks like:

:term:`jquery.autocomplete` プラグインに対するいくつかのオプションは
マッピングされ、ウィジェットに渡すことができます。利用可能なオプションに
関する詳細については、 :class:`deform.widget.AutocompleteInputWidget` を
参照してください。オプションの渡し方はこのようになります:


.. code-block:: python

   form['nobsfrozz'].widget = AutocompleteInputWidget(
				values=['spam, 'eggs', 'bar', 'baz'],
                                min_length=1)


.. See `http://deformdemo.repoze.org/autocomplete_input/
.. <http://deformdemo.repoze.org/autocomplete_input/>`_ and
.. `http://deformdemo.repoze.org/autocomplete_remote_input/
.. <http://deformdemo.repoze.org/autocomplete_remote_input/>`_ for
.. working examples. A working example of a remote URL providing
.. completion data can be found at
.. `http://deformdemo.repoze.org/autocomplete_input_values/
.. <http://deformdemo.repoze.org/autocomplete_input_values/>`_.

動作例については `http://deformdemo.repoze.org/autocomplete_input/
<http://deformdemo.repoze.org/autocomplete_input/>`_ と
`http://deformdemo.repoze.org/autocomplete_remote_input/
<http://deformdemo.repoze.org/autocomplete_remote_input/>`_ を参照
してください。補完データを提供する外部 URL の動作例は
`http://deformdemo.repoze.org/autocomplete_input_values/
<http://deformdemo.repoze.org/autocomplete_input_values/>`_ で見つける
ことができます。


.. Use of :class:`deform.widget.AutocompleteInputWidget` is not a
.. replacement for server-side validation of the field; it is purely a UI
.. affordance.  If the data must be checked at input time a separate
.. :term:`validator` should be attached to the related schema node.

:class:`deform.widget.AutocompleteInputWidget` を使用することは、
フィールドに対するサーバーサイドのバリデーションの代わりにはなりません;
それは純粋に UI アフォーダンスです。入力時にデータをチェックしなければ
ならない場合、関連するスキーマノードに :term:`validator` を取り付ける
必要があります。


.. Creating a New Schema Type

新しいスキーマ型の作成
--------------------------

.. Sometimes the default schema types offered by Colander may not be sufficient
.. to model all the structures in your application.  

場合によっては Colander によって提供されるデフォルトのスキーマ型が
アプリケーションのすべての構造をモデル化するのには不十分なことがあるかも
しれません。


.. If this happens, refer to the Colander documentation on
.. :ref:`colander:defining_a_new_type`.

これが問題になる場合は、 :ref:`colander:defining_a_new_type` の
Colander ドキュメンテーションを参照してください。
