.. _widget:

.. Widgets

ウィジェット
============

.. A widget is a bit of code that is willing to:

ウィジェットは、以下を行うことを意図したコード片です:


.. - serialize a :term:`cstruct` into HTML for display in a form
..   rendering

- :term:`cstruct` をフォームレンダリングにおける表示用の HTML に
  シリアライズする


.. - deserialize data obtained from a form post (a :term:`pstruct`) into
..   a data structure suitable for deserialization by a :term:`schema
..   node` (a :term:`cstruct`).

- フォーム送信から得られたデータ (:term:`pstruct`) を
  :term:`schema node` による逆シリアライズに適したデータ構造
  (:term:`cstruct`) に逆シリアライズする


.. - handle validation errors

- バリデーションエラーを扱う


.. Deform ships with a number of built-in widgets.  You hopefully needn't
.. create your own widget unless you're trying to do something that the
.. built-in widget set didn't anticipate.  However, when a built-in
.. Deform widget doesn't do exactly what you want, you can extend Deform
.. by creating a new widget that is more suitable for the task.

Deform は組み込みのウィジェットを多数同梱しています。組み込みのウィジェット
が予測していなかったことをしようとしているのでなければ、たいていは自作
のウィジェットは必要はありません。しかし、やりたいことが組み込みの
Deform ウィジェットだけでは実現できない場合、タスクにより適した新しい
ウィジェットを作成することで Deform を拡張することができます。


.. Widget Templates

ウィジェットテンプレート
------------------------

.. A widget needn't use a template file, but each of the built-in widgets
.. does.  A template is usually assigned to a default widget via its
.. ``template`` and ``readonly_template`` attributes; those attributes
.. are then used in the ``serialize`` method of the widget, ala:

ウィジェットはテンプレートファイルを使用する必要はありませんが、
組み込みのウィジェットのそれぞれはテンプレートファイルを使用しています。
テンプレートは通常、デフォルトウィジェットの ``template`` と
``readonly_template`` 属性によって割り当てられます; その後、それらの
属性はウィジェットの ``serialize`` メソッドの中で使用されます, ala:


.. code-block:: python
   :linenos:

    def serialize(self, field, cstruct, readonly=False):
        if cstruct in (null, None):
            cstruct = ''
        template = readonly and self.readonly_template or self.template
        return field.renderer(template, field=field, cstruct=cstruct)


.. The :meth:`deform.field.renderer` method is a method which accepts a
.. logical template name (such as ``texinput``) and renders it using the
.. active Deform :term:`renderer`; the default renderer is the ZPT
.. renderer, which uses the templates within the ``deform/templates``
.. directory within the :mod:`deform` package.  See :ref:`templates` for
.. more information about widget templates.

:meth:`deform.field.renderer` メソッドは、 (``texinput`` のような)
論理的なテンプレート名を受け取り、active な Deform :term:`renderer` を
使用してそれをレンダリングするメソッドです; デフォルトのレンダラーは
ZPT レンダラーです。それは、 :mod:`deform` パッケージ内にある
``deform/templates`` ディレクトリ内のテンプレートを使用します。
ウィジェットテンプレートについての詳細は、 :ref:`templates` を
参照してください。


.. Widget Javascript

ウィジェット Javascript
-----------------------

.. Some built-in Deform widgets require JavaScript.  In order for the
.. built-in Deform widgets that require JavaScript to function properly,
.. the ``deform.load()`` JavaScript function must be called when the
.. page containing a form is renderered.

いくつかの組み込みの Deform ウィジェットは JavaScript を要求します。
組み込みの Deform ウィジェットが正常に機能するために JavaScript を読み込む
ことができるように、フォームを含むページはレンダリングされる際に
``deform.load()`` JavaScript 関数を呼ばれなければなりません。


.. Some built-in Deform widgets include JavaScript which operates against
.. a local input element when it is loaded.  For example, the
.. :class:`deform.widget.AutocompleteInputWidget` template looks like
.. this:

いくつかの組み込みの Deform ウィジェットには、ロードされたときにローカルの
input 要素を操作する JavaScript が含まれます。例えば、
:class:`deform.widget.AutocompleteInputWidget` テンプレートはこのように
なります:


.. code-block:: html
   :linenos:

    <span tal:omit-tag="">
        <input type="text"
               name="${field.name}"
               value="${cstruct}" 
               tal:attributes="size field.widget.size;
                               class field.widget.css_class"
               id="${field.oid}"/>
        <script tal:condition="field.widget.values" type="text/javascript">
          deform.addCallback(
            '${field.oid}',
            function (oid) {
                $('#' + oid).autocomplete({source: ${values}});
                $('#' + oid).autocomplete("option", ${options});
            }
          );
        </script>
    </span>


.. ``field.oid`` refers to the ordered identifier that Deform gives to
.. each field widget rendering.  You can see that the script which runs
.. when this widget is included in a rendering calls a function named
.. ``deform.addCallback``, passing it the value of ``field.oid`` and a
.. callback function as ``oid`` and ``callback`` respectively.  When it
.. is executed, the callback function calls the ``autocomplete`` method
.. of the JQuery selector result for ``$('#' + oid)``.

``field.oid`` は、 ``Deform`` が各フィールドウィジェットをレンダリング
する際に与える順序付き識別子を参照します。このウィジェットがレンダリング
されるときに実行されるスクリプトが ``deform.addCallback`` という名前の
関数を呼び出すこと、その関数には ``field.oid`` の値とコールバック関数が
それぞれ ``oid`` と ``callback`` として渡されることが分かるでしょう。
コールバック関数は、実行されると ``$('#' + oid)`` に対する
jQuery セレクタ結果の ``autocomplete`` メソッドを呼び出します。


.. The callback define above will be called under two circumstances:

上で定義されたコールバックは、以下の2つの状況で呼ばれます:


.. - When the page first loads and the ``deform.load()`` JavaScript
..   function is called.

- ページが最初にロードされて ``deform.load()`` JavaScript 関数が呼ばれたとき。


.. - When a :term:`sequence` is involved, and a sequence item is added,
..   resulting in a call to the ``deform.addSequenceItem()`` JavaScript
..   function.

- :term:`sequence` が関係していて、シーケンス要素が追加されたとき。
  これは ``deform.addSequenceItem()`` JavaScript 関数の呼び出しの
  結果として起こります。


.. The reason that default Deform widgets call ``deform.addCallback``
.. rather than simply using ``${field.oid}`` directly in the rendered
.. script is becase sequence item handling happens entirely client side
.. by cloning an existing prototype node, and before a sequence item can
.. be added, all of the ``id`` attributes in the HTML that makes up the
.. field must be changed to be unique.  The ``addCallback`` indirection
.. assures that the callback is executed with the *modified* oid rather
.. than the protoype node's oid.  Your widgets should do the same if they
.. are meant to be used as part of sequences.

デフォルトの Deform ウィジェットが、レンダリングされたスクリプトの中で
単に直接 ``${field.oid}`` を使用するのではなく ``deform.addCallback``
を呼んでいる理由は、シーケンス要素の処理は既存のプロトタイプノードの
クローンを作ることによって完全にクライアント側で起こり、シーケンス要素が
追加できるようになる前にフィールドを構成する HTMLの すべての ``id`` 属性が
ユニークになるように変更しなければならないためです。 ``addCallback``
による間接性は、プロトタイプノードの oid ではなく *修正された* oid で
コールバックが実行されることを保証します。あなたのウィジェットが
シーケンスの一部として使用されることを意図している場合、同じように
しなければなりません。


.. Widget Requirements and Resources

.. _widget_requirements:

ウィジェットの要求 (requirement) とリソース
-------------------------------------------

.. Some widgets require external resources to work properly (such as CSS
.. and Javascript files).  Deform provides mechanisms that will allow you
.. to determine *which* resources are required by a particular form
.. rendering, so that your application may include them in the HEAD of
.. the page which includes the rendered form.

いくつかのウィジェットは、 (CSS や Javascript ファイルのような)
外部リソースが適切に働くことを必要とします。 Deform は、特定のフォーム
レンダリングで *どの* リソースが必要とされるかを決定するための
メカニズムを提供します。それにより、アプリケーションはレンダリングされた
フォームを含むページの HEAD にそれらのリソースをインクルードすることが
できます。


.. The (Low-Level) :meth:`deform.Field.get_widget_requirements` Method

.. _get_widget_requirements:

(低レベル) :meth:`deform.Field.get_widget_requirements` メソッド
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. After a form has been fully populated with widgets, the
.. :meth:`deform.Field.get_widget_requirements` method called on the form
.. object will return a sequence of two-tuples.  When a non-empty
.. sequence is returned by :meth:`deform.Field.get_widget_requirements`,
.. it means that one or more CSS or JavaScript resources will need to be
.. loaded by the page performing the form rendering in order for some
.. widget on the page to function properly.

ウィジェットとともにフォームが完全に実体化された後で、フォームオブジェクトに
対して :meth:`deform.Field.get_widget_requirements` メソッドを呼び出すと、
2 要素のタプルのシーケンスが返されます。
:meth:`deform.Field.get_widget_requirements` によって空でないシーケンス
が返される場合、それはフォームをレンダリングするページ上の何らかの
ウィジェットが適切に機能するために CSS あるいは JavaScript リソースを
ロードする必要があるということを意味します。


.. The first element in each two-tuple represents a *requirement name*.
.. It represents a logical reference to one *or more* Javascript or CSS
.. resources.  The second element in each two-tuple is the reqested
.. version of the requirement.  It may be ``None``, in which case the
.. version required is unspecified.  When the version required is
.. unspecified, a default version of the resource set will be chosen.

それぞれの2要素タプルの第1要素は *要求名* を表わします。
それは、1つ *または複数* の Javascript あるいは CSS リソースへの論理的
な参照を表現しています。それぞれの2要素タプルの第2要素は要求バージョンです。
それは ``None`` かもしれません。その場合には、要求バージョンは無指定です。
要求バージョンが無指定の場合、リソースセットのデフォルトバージョンが選択
されます。


.. The requirement name / version pair implies a set of resources, but it
.. is not a URL, nor is it a filename or a filename prefix.  The caller
.. of :meth:`deform.Field.get_widget_requirements` must use the resource
.. names returned as *logical* references.  For example, if the
.. requirement name is ``jquery``, and the version id is ``1.4.2``, the
.. caller can take that to mean that the JQuery library should be loaded
.. within the page header via, for example the inclusion of the HTML
.. ``<script type="text/javascript"
.. src="http://deformdemo.repoze.org/static/scripts/jquery-1.4.2.min.js"></script>``
.. within the HEAD tag of the rendered HTML page.

要求名/バージョンのペアは何らかのリソースの集合を示唆していますが、
それは URL ではなく、ファイル名やファイル名のプレフィックスでもありません。
:meth:`deform.Field.get_widget_requirements` の呼び出し元は
返されたリソース名を *論理的な* 参照として使用しなければなりません。
例えば、要求名が ``jquery`` でバージョン id が ``1.4.2`` の場合、
呼び出し元はページヘッダー内で jQuery ライブラリをロードする必要があると
解釈することができます  (例えばレンダリングされた HTML ページの HEAD タグ
内に HTML ``<script type="text/javascript"
src="http://deformdemo.repoze.org/static/scripts/jquery-1.4.2.min.js"></script>``
を挿入することによって)。


.. Users will almost certainly prefer to use the
.. :meth:`deform.Field.get_widget_resources` API (explained in the
.. succeeding section) to obtain a fully expanded list of relative
.. resource paths required by a form rendering.
.. :meth:`deform.Field.get_widget_requirements`, however, may be used if
.. custom requirement name to resource mappings need to be done without
.. the help of a :term:`resource registry`.

ユーザはほぼ確実に、フォームレンダリングで要求される相対的なリソースパスの
完全に展開されたリストを得るために
:meth:`deform.Field.get_widget_resources` API (次のセクションの中で説明
されます) を使用することを好むでしょう。しかし、カスタムな要求名から
リソースへのマッピングを :term:`resource registry` (リソースレジストリ) の
助けなしで行う必要がある場合、 :meth:`deform.Field.get_widget_requirements`
を使用することができます。


.. See also the description of ``requirements`` in
.. :class:`deform.Widget`.

:class:`deform.Widget` の ``requirements`` の説明も参照してください。


.. The (High-Level) :meth:`deform.Field.get_widget_resources` Method

.. _get_widget_resources:

(高レベル) :meth:`deform.Field.get_widget_resources` メソッド
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. A mechanism to resolve the requirements of a form into relative
.. resource filenames exists as a method:
.. :meth:`deform.Field.get_widget_resources`.

フォームの要求をリソースの相対ファイル名に解決するメカニズムが
存在します: :meth:`deform.Field.get_widget_resources` メソッドです。


.. note::

   .. Because Deform is framework-agnostic, this method only *reports* to
   .. its caller the resource paths required for a successful form
   .. rendering, it does not (cannot) arrange for the reported
   .. requirements to be satisfied in a page rendering; satisfying these
   .. requirements is the responsibility of the calling code.

   Deform はフレームワークについては関知しない (framework-agnostic) ので、
   このメソッドはフォームを正しくレンダリングするのに必要なリソースパスを
   単に呼び出し元に *報告* します。 Deform はページレンダリングに際して
   報告した要求が満たされるように準備したりはしません (できません);
   これらの要求を満たすことは、呼び出し元のコードの責任です。


.. The :meth:`deform.Field.get_widget_resources` method returns a
.. dictionary with two keys: ``js`` and ``css``.  The value related to
.. each key in the dictionary is a list of *relative* resource names.
.. Each resource name is assumed to be relative to the static directory
.. which houses your application's Deform resources (usually a copy of
.. the ``static`` directory inside the Deform package).  If the method is
.. called with no arguments, it will return a dictionary in the same form
.. representing resources it believes are required by the current form.
.. If it is called with a set of requirements (the value returned by the
.. :meth:`deform.Field.get_widget_requirements` method), it will attempt
.. to resolve the requirements passed to it.  You might use it like so:

:meth:`deform.Field.get_widget_resources` メソッドは、 ``js`` と ``css``
という2つのキーを持つ辞書を返します。辞書中の各キーと関係する値は
*相対的な* リソース名のリストです。それぞれのリソース名は、アプリケーション
の Deform リソースが含まれる静的ディレクトリ (通常 Deform パッケージ内部
の ``static`` ディレクトリのコピー) からの相対とみなされます。このメソッドを
引数なしで呼ぶと辞書を返します。この辞書は、現在のフォームによって要求
されるリソースを表すのと同じ形式です。このメソッドを要求のセット
(:meth:`deform.Field.get_widget_requirements` メソッドによって返された値)
を伴って呼ぶと、渡された要求を解決しようとします。このメソッドは
以下のように使うことができます:


.. code-block:: python
   :linenos:

   import deform

   form = deform.Form(someschema)
   resources = form.get_widget_resources()
   js_resources = resources['js']
   css_resources = resources['css']
   js_links = [ 'http://my.static.place/%s' % r for r in js_resources ]
   css_links = [ 'http://my.static.place/%s' % r for r in css_resources ]
   js_tags = ['<script type="text/javascript" src="%s"></script>' % link
              for link in js_links]
   css_tags = ['<link rel="stylesheet" href="%s"/>' % link
              for link in css_links]
   tags = js_tags + css_tags
   return {'form':form.render(), 'tags':tags}


.. The template rendering the return value would need to make sense of
.. "tags" (it would inject them wholesale into the HEAD).  Obviously,
.. other strategies for rendering HEAD tags can be devised using the
.. result of ``get_widget_resources``, this is just an example.

返り値をレンダリングするテンプレートは「タグ」の意味を理解する必要があります
(それは HEAD の中にタグをまとめて挿入するでしょう)。明らかに、
``get_widget_resources`` の結果を使用して HEAD タグをレンダリングするための
他の戦略を工夫することができます。これは単なる例です。

   
.. :meth:`deform.Field.get_widget_resources` uses a :term:`resource
.. registry` to map requirement names to resource paths.  If
.. :meth:`deform.Field.get_widget_resources` cannot resolve a requirement
.. name, or it cannot find a set of resources related to the supplied
.. *version* of the requirement name, an :exc:`ValueError` will be
.. raised.  When this happens, it means that the :term:`resource
.. registry` associated with the form cannot resolve a requirement name
.. or version.  When this happens, a resource registry that knows about
.. the requirement will need to be associated with the form explicitly,
.. e.g.:

:meth:`deform.Field.get_widget_resources` は、リソースパスに
要求名をマッピングするために :term:`resource registry` を使用
します。 :meth:`deform.Field.get_widget_resources` が要求名を
解決できない場合、または提供された要求名の *バージョン* に関連付けられた
リソースのセットを見つけることができない場合、
:exc:`ValueError` が送出されます。この例外が起こる場合、それはフォームに
関連付けられた :term:`resource registry` が要求名やバージョンを
解決できないということを意味します。この場合、要求のことを知っている
リソースレジストリを明示的にフォームに関連付ける必要があるでしょう。
例えば:


.. code-block:: python
   :linenos:

   registry = deform.widget.ResourceRegistry()
   registry.set_js_resources('requirement', 'ver', 'bar.js', 'baz.js')
   registry.set_css_resources('requirement', 'ver', 'foo.css', 'baz.css')

   form = Form(schema, resource_registry=registry)
   resources = form.get_widget_resources()
   js_resources = resources['js']
   css_resources = resources['css']
   js_links = [ 'http://my.static.place/%s' % r for r in js_resources ]
   css_links = [ 'http://my.static.place/%s' % r for r in css_resources ]
   js_tags = ['<script type="text/javascript" src="%s"></script>' % link
              for link in js_links]
   css_tags = ['<link type="text/css" href="%s"/>' % link
              for link in css_links]
   tags = js_tags + css_tags
   return {'form':form.render(), 'tags':tags}


.. An alternate default resource registry can be associated with *all*
.. forms by calling the
.. :meth:`deform.Field.set_default_resource_registry` class method:

:meth:`deform.Field.set_default_resource_regis` クラスメソッドを
呼び出すことにより、別のデフォルトのリソースレジストリを *すべての*
フォームと関連付けることができます:


.. code-block:: python
   :linenos:

   registry = deform.widget.ResourceRegistry()
   registry.set_js_resources('requirement', 'ver', 'bar.js', 'baz.js')
   registry.set_css_resources('requirement', 'ver', 'foo.css', 'baz.css')
   Form.set_default_resource_registry(registry)


.. This will result in the ``registry`` registry being used as the
.. default resource registry for all form instances created after the
.. call to ``set_default_resource_registry``, hopefully allowing resource
.. resolution to work properly again.

これによって ``registry`` レジストリが ``set_default_resource_registry`` の
呼び出しより後に生成されたすべてのフォームインスタンスでデフォルトの
リソースレジストリとして使用されることになります。これによって再び
リソース解決が適切に動作するようになることが期待できます。


.. See also the documentation of the ``resource_registry`` argument in
.. :class:`deform.Field` and the documentation of
.. :class:`deform.widget.ResourceRegistry`.

:class:`deform.Field` の ``resource_registry`` 引数の
ドキュメンテーションと、 :class:`deform.widget.ResourceRegistry` の
ドキュメンテーションも参照してください。


.. Specifying Widget Requirements

.. _specifying_widget_requirements:

ウィジェットの要求を指定する
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. When creating a new widget, you may specify its requirements by using
.. the ``requirements`` attribute:

新しいウィジェットを生成するときに、 ``requirements`` 属性を使用することで
要求を指定することができます:


.. code-block:: python
   :linenos:

   from deform.widget import Widget

   class MyWidget(Widget):
       requirements = ( ('jquery', '1.4.2'), )


.. There are no hard-and-fast rules about the composition of a
.. requirement name.  Your widget's docstring should explain what its
.. requirement names mean, and how map to the logical requirement name to
.. resource paths within a a :term:`resource registry`.  For example,
.. your docstring might have text like this: "This widget uses a library
.. name of ``jquery.tools`` in its requirements list.  The name
.. ``jquery.tools`` implies that the JQuery Tools library must be loaded
.. before rendering the HTML page containing any form which uses this
.. widget; JQuery Tools depends on JQuery, so JQuery should also be
.. loaded.  The widget expects JQuery Tools version X.X (as specified in
.. the version field), which expects JQuery version X.X to be loaded
.. previously.".  It might go on to explain that a set of resources need
.. to be added to a :term:`resource registry` in order to resolve the
.. logical ``jquery.tools`` name to a set of relative resource paths, and
.. that the resulting custom resource registry should be used when
.. constructing the form.  The default resource registry
.. (:attr:`deform.widget.resource_registry`) does not contain resource
.. mappings for your newly-created requirement.

要求名の構成に関して明確なルールはありません。ウィジェットの docstring は、
その要求名が何を意味するか、そして論理的な要求名からどのように
:term:`resource registry` 内のリソースパスにマッピングするかを説明するべきです。
例えば、 docstring にはこのようなテキストがあるかもしれません:
「このウィジェットは、要求リストの中にある ``jquery.tools`` の
ライブラリ名を使用します。名前 ``jquery.tools`` は、このウィジェットを
使用するフォームを含む HTML ページをレンダリングする前に
jQuery Tools ライブラリをロードしなければならないことを示唆します;
jQuery Tools は jQuery に依存します。したがって、 jQuery もロード
しなければなりません。ウィジェットは jQuery Tools バージョン X.X
(version フィールドで指定) を期待します。
それは、 jQuery バージョン X.X が以前にロードされることを期待します」。
そして次に、論理的な ``jquery.tools`` 名を相対的なリソースパスのセット
へ解決するためにリソースのセットを :term:`resource registry` に追加する
必要があり、その結果として生じるカスタムリソースレジストリをフォームを
構築する際に使用すべきであるということを説明するかもしれません。
デフォルトのリソースレジストリ (:attr:`deform.widget.resource_registry`) は、
新しく作られた要求のためのリソースのマッピングを含んでいません。


.. Writing Your Own Widget

.. _writing_a_widget:

独自ウィジェットを書く
-----------------------

.. Writing a Deform widget means creating an object that supplies the
.. notional Widget interface, which is described in the
.. :class:`deform.widget.Widget` class documentation.  The easiest way to
.. create something that implements this interface is to create a class
.. which inherits directly from the :class:`deform.widget.Widget` class
.. itself.

Deform ウィジェットを書くことは、概念的なウィジェットインタフェースを
提供するオブジェクトを作成することを意味します。そのインタフェースは
:class:`deform.widget.Widget` クラスのドキュメンテーションで述べられて
います。このインタフェースを実装するものを作成する最も簡単な方法は、
:class:`deform.widget.Widget` クラスそれ自体から直接継承するクラスを
作成することです。


.. The :class:`deform.widget.Widget` class has a concrete implementation
.. of a constructor and the ``handle_error`` method as well as default
.. values for all required attributes.  The :class:`deform.widget.Widget`
.. class also has abstract implementations of ``serialize`` and
.. ``deserialize`` each of which which raises a
.. :exc:`NotImplementedError` exception; these must be overridden by your
.. subclass; you may also optionally override the ``handle_error`` method
.. of the base class.

:class:`deform.widget.Widget` クラスは、コンストラクタと ``handle_error``
メソッドの具体的な実装を持ち、すべての必須の属性に対するデフォルト値を
持っています。 :class:`deform.widget.Widget` クラスは、さらに
``serialize`` と ``deserialize`` の抽象的な実装を持っています。(それぞれ
:exc:`NotImplementedError` 例外を送出します); サブクラスはこれらを
オーバーライドしなければなりません; さらに、任意で基底クラスの
``handle_error`` メソッドをオーバーライドすることもできます。


.. For example:

例えば:


.. code-block:: python
   :linenos:

    from deform.widget import Widget

    class MyInputWidget(Widget):
        def serialize(self, field, cstruct=None, readonly=False):
            ...

        def deserialize(self, field, pstruct=None):
            ...

        def handle_error(self, field, error):
            ...


.. We describe the ``serialize``, ``deserialize`` and ``handle_error``
.. methods below.

``serialize`` メソッド、 ``deserialize`` メソッド、 ``handle_error``
メソッドについて次に説明します。


.. The ``serialize`` Method

``serialize`` メソッド
~~~~~~~~~~~~~~~~~~~~~~~~

.. The ``serialize`` method of a widget must serialize a :term:`cstruct`
.. value to an HTML rendering.  A :term:`cstruct` value is the value
.. which results from a :term:`Colander` schema serialization for the
.. schema node associated with this widget.  The result of this method
.. should always be a ``unicode`` type containing some HTML.

ウィジェットの ``serialize`` メソッドは、 HTML レンダリングに対する
:term:`cstruct` 値をシリアライズしなければなりません。 :term:`cstruct` 値は、
このウィジェットに関連したスキーマノード用の :term:`Colander` スキーマ
シリアライズに起因する値です。このメソッドの結果は常にある HTML を含んでいる
``unicode`` 型でなければなりません。


.. The ``field`` argument passed to ``serialize`` is the :term:`field`
.. object to which this widget is attached.  Because a :term:`field`
.. object itself has a reference to the widget it uses (as
.. ``field.widget``), the field object is passed to the ``serialize``
.. method of the widget rather than the widget having a ``field``
.. attribute in order to avoid a circular reference.

``serialize`` に渡される ``field`` 引数は、このウィジェットが
取り付けられている :term:`field` オブジェクトです。
:term:`field` オブジェクト自身がそのフィールドを使用するウィジェットへの
参照を (``field.widget`` として) 持っているので、循環参照を回避するために
フィールドオブジェクトは ``field`` 属性を持つウィジェットではなく
ウィジェットの ``serialize`` メソッドに渡されます。


.. If the ``readonly`` argument passed to ``serialize`` is ``True``, it
.. indicates that the result of this serialization should be a read-only
.. rendering (no active form controls) of the ``cstruct`` data to HTML.

``serialize`` に渡された ``readonly`` 引数が ``True`` の場合、この
シリアライズの結果は ``cstruct`` データから HTML への読み出し専用の
レンダリングである (つまりアクティブなフォームコントロールがない) ことを
示します。


.. Let's pretend our new ``MyInputWidget`` only needs to create a text
.. input control during serialization.  Its ``serialize`` method might
.. get defined as so:

新しい ``MyInputWidget`` がシリアライズ中にテキスト入力コントロールを
作成することだけが必要としましょう。その ``serialize`` メソッドはこのように
定義されるかもしれません:


.. code-block:: python
   :linenos:

    from deform.widget import Widget
    from colander import null
    import cgi

    class MyInputWidget(Widget):
        def serialize(self, field, cstruct=None, readonly=False):
            if cstruct is null:
                cstruct = u''
            quoted = cgi.escape(cstruct, quote='"')
            return u'<input type="text" value="%s">' % quoted


.. Note that every ``serialize`` method is responsible for returning a
.. serialization, no matter whether it is provided data by its caller or
.. not.  Usually, the value of ``cstruct`` will contain appropriate data
.. that can be used directly by the widget's rendering logic.  But
.. sometimes it will be ``colander.null``.  It will be ``colander.null``
.. when a form which uses this widget is serialized without any data; for
.. example an "add form".

呼び出し元からデータを渡されたかどうかに関わらず、 ``serialize`` メソッド
が常にシリアライズ結果を返す責任を持つことに注意してください。通常
``cstruct`` 値は、ウィジェットのレンダリングロジックで直接使用することが
できる適切なデータを含みます。しかし、それは時々 ``colander.null`` に
なります。このウィジェットを使用するフォームがデータなしでシリアライズ
される時 (例えば "add form") 、 ``colander.null`` になるでしょう。


.. All widgets *must* check if the value passed as ``cstruct`` is the
.. ``colander.null`` sentinel value during ``serialize``.  Widgets are
.. responsible for handling this eventuality, often by serializing a
.. logically "empty" value.

すべてのウィジェットは、 ``serialize`` 中に ``cstruct`` として渡された値が
``colander.null`` 番兵値かどうかチェック *しなければなりません* 。
ウィジェットはこの不測の事態を扱うことに責任を持ちます。
それは、しばしば論理的な「空の」値をシリアライズすることにより行われます。


.. Regardless of how the widget attempts to compute the default value, it
.. must still be able to return a rendering when ``cstruct`` is
.. ``colander.null``.  In the example case above, the widget uses the
.. empty string as the ``cstruct`` value, which is appropriate for this
.. type of "scalar" input widget; for a more "structural" kind of widget
.. the default might be something else like an empty dictionary or list.

ウィジェットがデフォルト値をどのように計算しようとするかにかかわらず、
それは ``cstruct`` が ``colander.null`` である場合にもレンダリング結果を
返せなければなりません。上記の例の場合では、ウィジェットは ``cstruct``
値として空の文字列を使用しています。この種類の「スカラー」入力ウィジェット
では、それが適切です; より「構造的な」種類のウィジェットについては、
空の辞書やリストのような他の値がデフォルトになるかもしれません。


.. The ``MyInputWidget`` we created in the example does not use a
.. template. Any widget may use a template, but using one is not
.. required; whether a particular widget uses a template is really none
.. of Deform's business: deform simply expects a widget to return a
.. Unicode object containing HTML from the widget's ``serialize`` method;
.. it doesn't really much care how the widget creates that Unicode
.. object.

例の中で作成した ``MyInputWidget`` はテンプレートを使用しません。
あらゆるウィジェットはテンプレートを使用することができますが、その使用
は必須ではありません; 特定のウィジェットがテンプレートを使用するかどうかは、
Deform には関係ないことです: deform は、単にウィジェットの ``serialize``
メソッドが HTML を含んだ Unicode オブジェクトを返すことを期待します;
その Unicode オブジェクトをウィジェットがどのように作成するかについても
あまり考慮しません。


.. Each of the built-in Deform widgets (the widget implementations in
.. :mod:`deform.widget`) happens to use a template in order to make it
.. easier for people to override how each widget looks when rendered
.. without needing to change Deform-internal Python code.  Instead of
.. needing to change the Python code related to the widget itself, users
.. of the built-in widgets can often perform enough customization by
.. replacing the template associated with the built-in widget
.. implementation.  However, this is purely a convenience; templates are
.. largely a built-in widget set implementation detail, not an integral
.. part of the core Deform framework.

組み込みの Deform ウィジェット (:mod:`deform.widget` の中のウィジェット実装)
は、レンダリングされたときのウィジェットの見た目を Deform 内部の Python
コードを変更することなく簡単にオーバーライドできるように、たまたま
テンプレートを使用しています。ウィジェット自体に関係する Python コード
を変更する必要がある代わりに、組み込みのウィジェットのユーザは、しばしば
組み込みのウィジェット実装に関連付けられたテンプレートを交換することで
十分なカスタマイズを行なうことができます。しかしながら、これは純粋に
利便性のためです; テンプレートは、主に組み込みのウィジェットセットの実装詳細で、
コア Deform フレームワークにとって不可欠な部分ではありません。


.. Note that "scalar" widgets (widgets which represent a single value as
.. opposed to a collection of values) are not responsible for providing
.. "page furniture" such as a "Required" label or a surrounding div which
.. is used to provide error information when validation fails.  This is
.. the responsibility of the "structural" widget which is associated with
.. the parent field of the scalar widget's field (the "parent widget");
.. the parent widget is usually one of
.. :class:`deform.widget.MappingWidget` or
.. :class:`deform.widget.SequenceWidget`.

「スカラー」ウィジェット (値のコレクションではなく単一の値を表わすウィジェット)
は、「必須」ラベルや、バリデーションが失敗したときにエラー情報を提供するために
使用される周囲を囲む div のような「ページの装飾」を提供する責任を持たないことに
注意してください。これは、スカラーウィジェットフィールドの親フィールド
に関連付けられた「構造的な」ウィジェット(「親ウィジェット」) の責任です;
親ウィジェットは、通常 :class:`deform.widget.MappingWidget` または
:class:`deform.widget.SequenceWidget` のうちのどちらかです。


.. The ``deserialize`` Method

``deserialize`` メソッド
~~~~~~~~~~~~~~~~~~~~~~~~~~

.. The ``deserialize`` method of a widget must deserialize a
.. :term:`pstruct` value to a :term:`cstruct` value and return the
.. :term:`cstruct` value.  The ``pstruct`` argument is a value resulting
.. from the ``parse`` method of the :term:`Peppercorn` package. The
.. ``field`` argument is the field object to which this widget is
.. attached.

ウィジェットの ``deserialize`` メソッドは、 :term:`pstruct` 値を
:term:`cstruct` 値に逆シリアライズして :term:`cstruct` 値を返さなければ
なりません。 ``pstruct`` 引数は、 :term:`Peppercorn` パッケージの
``parse`` メソッドに起因する値です。 ``field`` 引数はこのウィジェットが
取り付けられているフィールドオブジェクトです。


.. code-block:: python
   :linenos:

    from deform.widget import Widget
    from colander import null
    import cgi

    class MyInputWidget(Widget):
        def serialize(self, field, cstruct, readonly=False):
            if cstruct is null:
                cstruct = u''
            return '<input type="text" value="%s">' % cgi.escape(cstruct)

        def deserialize(self, field, pstruct):
            if pstruct is null:
                return null
            return pstruct


.. Note that the ``deserialize`` method of a widget must, like
.. ``serialize``, deal with the possibility of being handed a
.. ``colander.null`` value.  ``colander.null`` will be passed to the
.. widget when a value is missing from the pstruct. The widget usually
.. handles being passed a ``colander.null`` value in ``deserialize`` by
.. returning `colander.null``, which signifies to the underlying schema
.. that the default value for the schema node should be used if it
.. exists.

ウィジェットの ``deserialize`` メソッドが (``serialize`` と同じように)
``colander.null`` 値が渡される可能性に対処しなければならないことに注意
してください。 pstruct に値が含まれない場合、ウィジェットに
``colander.null`` が渡されます。ウィジェットは通常 ``deserialize`` に
``colander.null`` 値が渡された場合 ``colander.null`` を返すことで対処
します。それは、 underlying スキーマに対してスキーマノードに対する
デフォルト値が存在するなら使用されるべきであることを示します。


.. The only other real constraint of the deserialize method is that the
.. ``serialize`` method must be able to reserialize the return value of
.. ``deserialize``.

deserialize メソッドに対する他のもう一つの実際の制約は、 ``serialize``
メソッドが ``deserialize`` の返り値を再シリアライズできなければならない、
というものです。


.. The ``handle_error`` Method

``handle_error`` メソッド
~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. The :class:`deform.widget.Widget` class already has a suitable
.. implementation; if you subclass from :class:`deform.widget.Widget`,
.. overriding the default implementation is not necessary unless you need
.. special error-handling behavior.

:class:`deform.widget.Widget` クラスは既に適切な実装を持っています;
:class:`deform.widget.Widget` からサブクラス化する場合、特別な
エラーハンドリングの振る舞いが必要なければ、デフォルト実装のオーバーライドは
必須ではありません。


.. Here's an implementation of the
.. :meth:`deform.widget.Widget.handle_error` method in the MyInputWidget
.. class:

これは、 MyInputWidget クラスでの :meth:`deform.widget.Widget.handle_error`
メソッドの実装です:


.. code-block:: python
   :linenos:

    from deform.widget import Widget
    from colander import null
    import cgi

    class MyInputWidget(Widget):
        def serialize(self, field, cstruct, readonly=False):
            if cstruct is null:
                cstruct = u''
            return '<input type="text" value="%s">' % cgi.escape(cstruct)

        def deserialize(self, field, pstruct):
            if pstruct is null:
                return null
            return pstruct

        def handle_error(self, field, error):
            if field.error is None:
                field.error = error
            for e in error.children:
                for num, subfield in enumerate(field.children):
                    if e.pos == num:
                        subfield.widget.handle_error(subfield, e)

.. The ``handle_error`` method of a widget must:

ウィジェットの ``handle_error`` メソッドは以下のことをしなければなりません:


.. - Set the ``error`` attribute of the ``field`` object it is passed if
..   the ``error`` attribute has not already been set.

- 渡された ``field`` オブジェクトの ``error`` 属性をセットする
  (まだ ``error`` 属性がセットされていなかった場合)。


.. - Call the ``handle_error`` methods of any subfields which
..   also have errors.

- エラーがあるすべてのサブフィールドの ``handle_error`` メソッドを呼び出す。


.. The ability to override ``handle_error`` exists purely for advanced
.. tasks, such as presenting all child errors of a field on a parent
.. field.  For example:

``handle_error`` をオーバーライドできる能力は、 (親フィールドに子のエラー
をすべて表示するような) 純粋に高度なタスクのために存在します。例えば:


.. code-block:: python
   :linenos:

    def handle_error(self, field, error):
        msgs = []
        if error.msg:
            field.error = error
        else:
            for e in error.children:
                msgs.append('line %s: %s' % (e.pos+1, e))
            field.error = Invalid(field.schema, '\n'.join(msgs))


.. This implementation does not attach any errors to field children;
.. instead it attaches all of the child errors to the field itself for
.. review.

この実装は、フィールドの子にエラーを表示しません; 代わりに、レビューのために
すべての子のエラーをフィールド自体に表示します。


.. The Template

テンプレート
~~~~~~~~~~~~

.. The template you use to render a widget will receive input from the
.. widget object, including ``field``, which will be the field object
.. represented by the widget.  It will usually use the ``field.name``
.. value as the ``name`` input element of the primary control in the
.. widget, and the ``field.oid`` value as the ``id`` element of the
.. primary control in the widget.

ウィジェットをレンダリングするために使用されるテンプレートは、ウィジェット
オブジェクトから入力を受け取ります。これには ``field`` が含まれ、それは
ウィジェットによって表現されるフィールドオブジェクトになるでしょう。
それは、通常 ``field.name`` 値をウィジェットの中の最初のコントロールの
``name`` 入力要素として 使用し、 ``field.oid`` 値をウィジェットの中の
最初のコントロールの ``id`` 要素として使用します。
