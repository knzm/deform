.. Using Ajax Forms

Ajax フォームを使う
===================

.. To create a form object that uses AJAX, we do this:

AJAX を使用するフォームオブジェクトを作成するためには、こうします:


.. code-block:: python
   :linenos:

   from deform import Form
   myform = Form(schema, buttons=('submit',), use_ajax=True)


.. :ref:`creating_a_form` indicates how to create a Form object based on
.. a schema and some buttons.  Creating an AJAX form uses the same
.. constructor as creating a non-AJAX form: the only difference between
.. the example provided in the :ref:`creating_a_form` section and the
.. example above of creating an AJAX form is the additional
.. ``use_ajax=True`` argument passed to the Form constructor.

:ref:`creating_a_form` は、スキーマといくつかのボタンに基づいてフォーム
オブジェクトを作成する方法を示しています。AJAX フォームの作成は、
非 AJAX フォームの作成と同じコンストラクタを使用します:
:ref:`creating_a_form` 節で提供される例と、上記の AJAX フォームを
作成する例の間の唯一の違いは、フォームコンストラクタに渡された追加の
``use_ajax=True`` 引数です。


.. If ``use_ajax`` is passed as ``True`` to the constructor of a
.. :class:`deform.Form` object, the form page is rendered in such a way
.. that when a submit button is pressed, the page is not reloaded.
.. Instead, the form is posted, and the result of the post replaces the
.. form element's DOM node.

:class:`deform.Form` オブジェクトのコンストラクタに ``use_ajax`` が
``True`` として渡される場合、 submit ボタンが押された時にページが
リロードされないようにフォームページがレンダリングされます。代わりに、
フォームは post され、 post の結果はフォーム要素の DOM ノードを置換します。


.. Examples of using the AJAX facilities in Deform are showcased on the
.. `http://deformdemo.repoze.org <http://deformdemo.repoze.org>`_
.. demonstration website:

Deform の AJAX 機能を使用する例は、デモサイト
`http://deformdemo.repoze.org <http://deformdemo.repoze.org>`_
上で見られます:


.. - `Redirection on validation success
..   <http://deformdemo.repoze.org/ajaxform/>`_

.. - `No redirection on validation success
..   <http://deformdemo.repoze.org/ajaxform/>`_

- `バリデーション成功時にリダイレクトする
  <http://deformdemo.repoze.org/ajaxform_redirect/>`_

- `バリデーション成功時にリダイレクトしない
  <http://deformdemo.repoze.org/ajaxform/>`_


.. Note that for AJAX forms to work, the ``deform.js`` and
.. ``jquery.form.js`` libraries must be included in the rendering of the
.. page that includes the form itself, and the ``deform.load()``
.. JavaScript function must be called by the rendering in order to
.. associate the form with AJAX.  This is the responsibility of the
.. wrapping page.  Both libraries are present in the ``static`` directory
.. of the :mod:`deform` package itself.  See :ref:`widget_requirements`
.. for a way to detect which JavaScript libraries are required for a
.. particular form rendering.

AJAX フォームが動くためには、フォーム自体を含むページのレンダリングに
``deform.js`` と ``jquery.form.js`` ライブラリが含まれなければならず、
AJAX にフォームを関連させるために ``deform.load()`` JavaScript 関数が
レンダリングによって呼ばれる必要があることに注意してください。これは
フォームを表示するページの責任です。両方のライブラリは :mod:`deform`
パッケージ自体の ``static`` ディレクトリの中にあります。特定の
フォームレンダリングにどの JavaScript ライブラリが必要かを検知する
方法については :ref:`widget_requirements` を参照してください。

