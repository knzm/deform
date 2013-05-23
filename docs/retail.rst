.. Retail Form Rendering

リテール・フォームレンダリング
==============================

.. In the previous chapter we demonstrated how to use Deform to render a complete
.. form, including the input fields, the buttons, and so forth.  We used the
.. :meth:`deform.Field.render()` method, and injected the resulting HTML snippet
.. into a larger HTML page in our application.  That is an effective and quick way
.. to put a form on a page, but sometimes you need more fine-grained control over
.. the way form HTML is rendered.  For example, you may need form elements to be
.. placed on the page side-by-side or you might need the form's styling to be
.. radically different than the form styling used by the default rendering of
.. Deform forms.  Often it's easier to use Deform slightly differently, where you
.. do more work yourself to draw the form within a template, and only use Deform
.. for some of its features.  We refer to this as "retail form rendering".

前章では、 input フィールドやボタンなどを含む完全なフォームをレンダリング
するために Deform を使用する方法をデモンストレーションしました。そこでは
:meth:`deform.Field.render()` メソッドを使用し、その結果として生じる
HTML 断片をアプリケーションのより大きな HTML ページに埋め込みました。
これは、ページにフォームを置くための効率的で迅速な方法ですが、
フォーム HTML をレンダリングする方法に関してよりきめの細かい制御が必要
になることもあります。例えば、フォーム要素がページに水平に配置される
ようにする必要があるかもしれません。あるいは Deform フォームのデフォルトの
レンダリングによって使用されているフォームスタイルとは根本的に異なる
フォームスタイルを必要とするかもしれません。多くの場合、 Deform をわずかに
異なるやり方で使用する方が簡単です。その場合、テンプレート内で
フォームを表示するために自分でより多くの仕事をします。そして Deform の機能
のうちのいくつかだけを使用します。私たちはこれを「リテール・フォーム
レンダリング」と呼びます (訳注: retail は「小売」の意味)。


.. note::

   .. This feature is new as of Deform 0.9.6.

   これは Deform 0.9.6 からの新機能です。


.. A Basic Example

基本的な例
---------------

.. Our schema and form object:

ここで使うスキーマとフォームオブジェクトです:


.. code-block:: python
   :linenos:

   import colander

   class Person(colander.MappingSchema):
       name = colander.SchemaNode(colander.String())
       age = colander.SchemaNode(colander.Integer(),
                                 validator=colander.Range(0, 200))

   schema = Person()
   form = deform.Form(schema, resource_registry=resource_registry)


.. We feed the schema into a template as the ``form`` value.  It doesn't matter
.. what kind of templating system you use to do this, but this example will use
.. ZPT.  Below, the name ``form`` refers to the form we just created above:

スキーマを ``form`` 値としてテンプレートに渡します。これをするのに
どんな種類のテンプレートシステムを使用するかは重要ではありませんが、
この例では ZPT を使用します。以下では、名前 ``form`` は、たった今生成した
フォームを指しています:


.. code-block:: xml

     <div class="row"
          tal:repeat="field form">
         <div class="span2">
             ${structure:field.title}
             <span class="req" tal:condition="field.required">*</span>
         </div>
         <div class="span2">
             ${structure:field.serialize()}
         </div>
         <ul tal:condition="field.error">
             <li tal:repeat="error field.error.messages()">
                 ${structure:error}
             </li>
         </ul>
     </div>


.. The above template iterates over the fields in the form, using the attributes of
.. each field to draw the title.

上記のテンプレートは、フォームのフィールドをイテレートして、
タイトルを表示するために各フィールドの属性を使用します。


.. You can use the ``__getitem__`` method of a form to grab named form fields
.. instead of iterating over all of its fields.  For example:

すべてのフィールドをイテレートする代わりに、名前の付いたフォームフィールドを
取得するためにフォームの ``__getitem__`` メソッドを使用することができます。
例えば:


.. code-block:: xml

     <div tal:define="field form['name']">
         <div class="span2">
             ${structure:field.title}
             <span class="req" tal:condition="field.required">*</span>
         </div>
         <div class="span2">
             ${structure:field.serialize()}
         </div>
         <ul tal:condition="field.error">
             <li tal:repeat="error field.error.messages()">
                 ${structure:error}
             </li>
         </ul>
     </div>


.. You can use as little or as much of the Deform Field API to draw the widget as
.. you like.  The above examples use the :meth:`deform.Field.serialize` method,
.. which is an easy way to let Deform draw the field HTML, but you can draw it
.. yourself instead if you like, and just rely on the field object for its
.. validation errors (if any).  Note that the ``serialize`` method accepts
.. arbitrary keyword arguments that will be passed as top-level arguments to the
.. Deform widget templates, so if you need to change how a particular widget is
.. rendered without doing things completely by hand, you may want to take a look
.. at the existing widget template and see if your need has been anticipated.

ウィジェットを表示するために Deform フィールド API をいくらでも使用できます。
上記の例は :meth:`deform.Field.serialize` メソッドを使用します。
それは Deform にフィールド HTML を表示させる簡単な方法ですが、
もしそうしたければ、代わりに自分自身でそれを表示して、(もしあれば) その
バリデーションエラーのためだけにフィールド・オブジェクトを頼ることができます。
``serialize`` メソッドが、 Deform ウィジェットテンプレートにトップレベルの
引数として渡される任意のキーワード引数を受け取ることに注意してください。
したがって、完全に手動でやることなく特定のウィジェットがレンダリングされる
方法を変更する必要があれば、既存のウィジェットテンプレートを見て、
あなたの要求が期待されているものかどうか確かめると良いでしょう。


.. In the POST handler for the form, just do things like we did in the last
.. chapter, except if validation fails, just re-render the template with the same
.. form object.

このフォームの POST ハンドラの中で、前章で行ったのと同様のことをします。
ただし、バリデーションが失敗した場合は単に同じフォームオブジェクトで再度
テンプレートをレンダリングします。


.. code-block:: python

       controls = request.POST.items() # get the form controls

       try:
           appstruct = form.validate(controls)  # call validate
       except ValidationFailure, e: # catch the exception
            # .. rerender the form .. its field's .error attributes
            # will be set


.. It is also possible to pass an ``appstruct`` argument to the
.. :class:`deform.Form` constructor to create "edit forms".  Form/field objects
.. are initialized with this appstruct (recursively) when they're created.  This
.. means that accessing ``form.cstruct`` will return the current set of rendering
.. values.  This value is reset during validation, so after a validation is done
.. you can re-render the form to show validation errors.

さらに、 "edit フォーム" を作成する :class:`deform.Form` コンストラクタに
``appstruct`` 引数を渡すことも可能です。フォーム/フィールドオブジェクトは、
生成される時にこの appstruct で (再帰的に) 初期化されます。これは、
``form.cstruct`` へのアクセスが現在のレンダリング値のセットを返すだろう
ということを意味します。この値はバリデーション中にリセットされます。
したがって、バリデーションが終わった後で、バリデーションエラーを示すために
再度フォームをレンダリングすることができます。


.. Note that existing Deform widgets are all built using "retail mode" APIs, so if
.. you need examples, you can look at their templates.

既存の Deform ウィジェットはすべて「リテールモード」 API を使用して構築
されていることに注意してください。したがって、例が必要ならそれらの
テンプレートを見ることができます。


.. Other methods that might be useful during retail form rendering are:

リテールフォームレンダリングにおいて役立つかもしれないその他のメソッドは
次の通りです:


.. - :meth:`deform.Field.__contains__`

.. - :meth:`deform.Field.start_mapping`

.. - :meth:`deform.Field.end_mapping`

.. - :meth:`deform.Field.start_sequence`

.. - :meth:`deform.Field.end_sequence`

.. - :meth:`deform.Field.start_rename`

.. - :meth:`deform.Field.end_rename`

.. - :meth:`deform.Field.set_appstruct`

.. - :meth:`deform.Field.set_pstruct`

.. - :meth:`deform.Field.render_template`

.. - :meth:`deform.Field.validate_pstruct` (and the ``subcontrol`` argument to
..   :meth:`deform.Field.validate`)

- :meth:`deform.Field.__contains__`

- :meth:`deform.Field.start_mapping`

- :meth:`deform.Field.end_mapping`

- :meth:`deform.Field.start_sequence`

- :meth:`deform.Field.end_sequence`

- :meth:`deform.Field.start_rename`

- :meth:`deform.Field.end_rename`

- :meth:`deform.Field.set_appstruct`

- :meth:`deform.Field.set_pstruct`

- :meth:`deform.Field.render_template`

- :meth:`deform.Field.validate_pstruct` (と :meth:`deform.Field.validate`
  に対する ``subcontrol`` 引数)


