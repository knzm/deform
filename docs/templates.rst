.. Templates

.. _templates:

テンプレート
============

.. A set of :term:`Chameleon` templates is used by the default widget set
.. present in :mod:`deform` to make it easier to customize the look and
.. feel of form renderings.

フォームレンダリングの見た目をカスタマイズしやすくするために、
:term:`Chameleon` テンプレートのセットが :mod:`deform` の中に存在する
デフォルトウィジェットセットによって使用されます。


.. Overriding the default templates

デフォルトテンプレートをオーバーライドする
------------------------------------------

.. The default widget set uses templates that live in the ``templates``
.. directory of the :mod:`deform` package. If you are comfortable using
.. the :term:`Chameleon` templating system, but you simply need to
.. override some of these templates you can create your own template
.. directory and copy the template you wish to customize into it. You can
.. then either configure your new template directory to be used for all
.. forms or for specific forms as described below.

デフォルトウィジェットは :mod:`deform` パッケージの ``templates``
ディレクトリにあるテンプレートを使用します。 :term:`Chameleon` テンプレート
システムを使用することに満足していて、これらのテンプレートのうちの
いくつかだけをオーバーライドする必要がある場合、自分のテンプレート
ディレクトリを作成して、カスタマイズしたいテンプレートをそこにコピー
することができます。そして、新しいテンプレートディレクトリをすべての
フォームで使用するように設定するか、あるいは下記に述べられているように
特定のフォームだけで使用することができます。


.. For relevant API documentation see the
.. :class:`deform.ZPTRendererFactory` class and the :class:`deform.Field`
.. class ``renderer`` argument.

適切な API ドキュメンテーションのために :class:`deform.ZPTRendererFactory`
クラスおよび :class:`deform.Field` クラスの ``renderer`` 引数を見てください。


.. Overriding for all forms

すべてのフォームでオーバーライドする
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. To globally override templates use the
.. :meth:`deform.Field.set_zpt_renderer` class method to change the
.. settings associated with the default ZPT renderer:

テンプレートを全体的にオーバーライドするには、デフォルト ZPT レンダラー
に関連した設定を変更する :meth:`deform.Field.set_zpt_renderer`
クラスメソッドを使用してください:


.. code-block:: python

   from pkg_resources import resource_filename
   from deform import Form

   deform_templates = resource_filename('deform', 'templates')
   search_path = ('/path/to/my/templates', deform_templates)

   Form.set_zpt_renderer(search_path)


.. Now, the templates in ``/path/to/my/templates`` will be used in
.. preference to the default templates whenever a form is rendered.
.. Any number of template directories can be put into the search path and
.. will be searched in the order specified with the first matching
.. template found being used.

これによって、フォームがレンダリングされる場合は常に
``/path/to/my/templates`` にあるテンプレートがデフォルトテンプレート
より優先して使用されるようになります。検索パスには任意の数のテンプレート
ディレクトリを入れることができます。それらは順に検索されて、最初に
見つかったテンプレートが使われます。


.. Overriding for specific forms

特定のフォームでオーバーライドする
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. If you only want to change the templates used for a specific form, or
.. even for the specific rendering of a form, you can pass a ``renderer``
.. argument to the :class:`deform.Form` constructor, e.g.:

特定のフォームだけで、あるいはフォームの特定のレンダリングだけで
使用されるテンプレートを変更したい場合は、 :class:`deform.Form`
コンストラクタに ``renderer`` 引数を渡すことができます、例えば:


.. code-block:: python

   from deform import ZPTRendererFactory
   from deform import Form
   from pkg_resources import resource_filename

   deform_templates = resource_filename('deform', 'templates')
   search_path = ('/path/to/my/templates', deform_templates)
   renderer = ZPTRendererFactory(search_path)

   form = Form(someschema, renderer=renderer)


.. When the above form is rendered, the templates in
.. ``/path/to/my/templates`` will be used in  preference to the default
.. templates. Any number of template directories can be put into the
.. search path and will be searched in the order specified with the first
.. matching template found being used.

上記のフォームがレンダリングされる時に、 ``/path/to/my/templates`` に
あるテンプレートがデフォルトテンプレートより優先して使用されます。
検索パスには任意の数のテンプレートディレクトリを入れることができます。
それらは順に検索されて、最初に見つかったテンプレートが使われます。


.. Using an alternative templating system

.. _creating_a_renderer:

別のテンプレートシステムを使う
--------------------------------------

.. A :term:`renderer` is used by the each widget implementation in
.. :mod:`deform` to render HTML from a set of templates. By default, each
.. of the default Deform widgets uses a template written in the Chameleon
.. ZPT templating language. If you'd rather use a different templating
.. system for your widgets, you can. To do so, you need to:

:term:`renderer` は、 deform の各ウィジェット実装によってテンプレートの
セットから HTML をレンダリングするために使用されます。デフォルトでは、
デフォルト Deform ウィジェットのそれぞれは Chameleon ZPT テンプレート
言語で書かれたテンプレートを使用します。ウィジェットで異なるテンプレート
システムを使用したければ、それは可能です。そのためには次のことが必要です:


.. - Write an alternate renderer that uses the templating system of your
..   choice.

- 好みのテンプレートシステムを使用する代替レンダラーを書く。


.. - Optionally, convert all the existing Deform templates to your
..   templating language of choice.  This is only necessary if you choose
..   to use the widgets that ship as part of Deform.

- 任意で、既存のすべての Deform テンプレートを選択したテンプレート言語
  に変換する。これは、 Deform の一部として付属しているウィジェットを
  使用することを選んだ場合にのみ必要です。


.. - Set the default renderer of the :class:`deform.Form` class.

- :class:`deform.Form` クラスのデフォルトレンダラーをセットする。


.. Creating a Renderer

レンダラーを作成する
~~~~~~~~~~~~~~~~~~~~

.. A renderer is simply a callable that accepts a single positional
.. argument, which is the template name, and a set of keyword arguments.
.. The keyword arguments it will receive are arbitrary, and differ per
.. widget, but the keywords usually include ``field``, a :term:`field`
.. object, and ``cstruct``, the data structure related to the field that
.. must be rendered by the template itself.

レンダラーは、単に1つの位置引数 (テンプレート名) とキーワード引数を
受け取る callable です。レンダラーが受け取るキーワード引数は任意で、
ウィジェット毎に異なります。しかし、キーワードは通常 ``field``
(:term:`field` オブジェクト) と ``cstruct`` (テンプレート自身によって
レンダリングしなければならない、フィールドと関係するデータ構造) を
含んでいます。


.. Here's an example of a (naive) renderer that uses the Mako templating
.. engine:

これは、 Mako テンプレートエンジンを使用する (素朴な) レンダラーの例です:


.. code-block:: python
   :linenos:


   from mako.template import Template

   def mako_renderer(tmpl_name, **kw):
       template = Template(filename='/template_dir/%s.mak' % tmpl_name)
       return template.render(**kw)

.. .. note:: A more robust implementation might use a template loader
..    that does some caching, or it might allow the template directory to
..    be configured.

.. note::

  より頑健な実装では、なんらかのキャッシュを行うテンプレートローダーを
  使用したり、テンプレートディレクトリが設定可能になっていたりする
  かもしれません。


.. Note the ``mako_renderer`` function we've created actually appends a
.. ``.mak`` extension to the ``tmpl_name`` it is passed.  This is because
.. Deform passes a template name without any extension to allow for
.. different templating systems to be used as renderers.

作成した ``mako_renderer`` 関数が、実際は渡された ``tmpl_name`` に
``.mak`` 拡張子を追加していることに注意してください。これは、異なる
テンプレートシステムをレンダラーとして使用することができるように、
Deform が拡張子のないテンプレート名を渡すからです。


.. Our ``mako_renderer`` renderer is now ready to have some templates
.. created for it.

``mako_renderer`` レンダラーは、すでにテンプレートを作り始めることが
できる状態になっています。


.. Converting the Default Deform Templates

デフォルトの Deform テンプレートを変換する
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. The :mod:`deform` package contains a directory named ``templates``.  You can
.. see the current trunk contents of this directory by `browsing the source
.. repository
.. <https://github.com/Pylons/deform/tree/master/deform/templates>`_. Each file
.. within this directory and any of its subdirectories is a Chameleon ZPT
.. template that is used by a default Deform widget.

:mod:`deform` パッケージには ``templates`` という名前のディレクトリが
含まれています。 `ソースリポジトリのブラウジング
<https://github.com/Pylons/deform/tree/master/deform/templates>`_ によって、
このディレクトリの現在の trunk の内容を見ることができます。このディレクトリと
サブディレクトリの中にあるファイルはどれも、デフォルト Deform ウィジェットに
よって使用される Chameleon ZPT テンプレートです。


.. For example, ``textinput.pt`` ZPT template, which is used by the
.. :class:`deform.widget.TextInputWidget` widget and which renders a text
.. input control looks like this:

例えば ``textinput.pt`` ZPT テンプレートはこのようになります (それは
:class:`deform.widget.TextInputWidget` ウィジェットによって使用され、
テキスト入力コントロールをレンダリングします):


.. literalinclude:: ../deform/templates/textinput.pt
   :language: xml
   :linenos:


.. If we created a Mako renderer, we would need to create an analogue of
.. this template.  Such an analogue should be named ``textinput.mak`` and
.. might look like this:

もし Mako レンダラーを作成していれば、このテンプレートの類似物を作成する
必要があるでしょう。それは ``textinput.mak`` と命名されるべきで、
このようになります:


.. code-block:: text
   :linenos:

   <input type="text" name="${field.name}" value="${cstruct}"
   % if field.widget.size:
   size=${field.widget.size}
   % endif
   />


.. Whatever the body of the template looks like, the resulting
.. ``textinput.mak`` should be placed in a directory that is meant to
.. house other Mako template files which are going to be consumed by
.. Deform.  You'll need to convert each of the templates that exist in
.. the Deform ``templates`` directory and its subdirectories, and put all
.. of the resulting templates into your private mako ``templates`` dir
.. too, retaining any directory structure (e.g., retaining the fact that
.. there is a ``readonly`` directory and converting its contents).

テンプレートの本体が何であっても、結果として得られる ``textinput.mak`` は
Deform によって使われる他の Mako テンプレートファイルを格納するための
ディレクトリに置かれるべきです。 Deform の ``templates`` ディレクトリと
そのサブディレクトリに存在するテンプレートそれぞれを変換する必要が
あるでしょう。また、結果として得られるすべてのテンプレートを、ディレクトリ
構造も保持したままプライベートの mako ``templates`` ディレクトリに入れる
必要があるでしょう (例えば ``readonly`` ディレクトリがあるという事実を
保持しながらその内容を変換する)。


.. Configuring Your New Renderer as the Default

新しいレンダラーをデフォルトに設定する
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. Once you've created a new renderer and created templates that match
.. all the existing Deform templates, you can now configure your renderer
.. to be used by Deform.  In startup code, add something like:

新しいレンダラーを作成して既存のすべての Deform テンプレートに対応する
テンプレートを作成したら、そのレンダラーが Deform によって使用されるように
設定することができます。初期化コードに以下のようなコードを追加してください:


.. code-block:: python
   :linenos:

   from mymakorenderer import mako_renderer

   from deform import Form
   Form.set_default_renderer(mako_renderer)


.. The deform widget system will now use your renderer as the default
.. renderer.

これで、 deform ウィジェットシステムは、デフォルトレンダラーとしてあなたの
レンダラーを使用するようになります。


.. Note that calling :meth:`deform.Field.set_default_renderer` will cause this
.. renderer to be used by default by all consumers in the process it's invoked
.. in.  This is potentially undesirable: you may need the same process to use
.. more than one renderer perhaps because that same process houses two different
.. Deform-using systems.  In this case, instead of using the
.. ``set_default_renderer`` method, you can write your application in such a way
.. that it passes a renderer to the Form constructor:

:meth:`deform.Field.set_default_renderer` を呼び出すと、それを起動した
同一プロセス内のすべての場所で、このレンダラーがデフォルトとして
使われるようになることに注意してください。これは潜在的に望ましくありません:
たとえば同じプロセスが Deform を使用する 2 つの異なるシステムを収容
しているといった理由で、同一プロセスで複数のレンダラーを使用する必要
があるかもしれません。この場合、 ``set_default_renderer`` メソッドを
使用する代わりに、アプリケーションが Form コンストラクタにレンダラーを
渡すように書くことができます:


.. code-block:: python
   :linenos:

   from mymakorenderer import mako_renderer
   from deform import Form

   ...
   schema = SomeSchema()
   form = Form(schema, renderer=mako_renderer)

