.. Deform Components

Deform コンポーネント
=====================

.. A developer dealing with Deform has to understand a few fundamental
.. types of objects and their relationships to one another.  These types
.. are:

Deform を扱う開発者は、少数の基本的な型のオブジェクトとそれぞれの関係を
理解する必要があります。それらの型は次の通りです:


.. - schema nodes

.. - field objects

.. - widgets

- スキーマノード

- フィールドオブジェクト

- ウィジェット


.. The Relationship Between Widgets, Fields, and Schema Objects

ウィジェット、フィールド、スキーマ・オブジェクトの関係
------------------------------------------------------------

.. The relationship between widgets, fields, and schema node objects is
.. as follows:

ウィジェット、フィールド、スキーマノード・オブジェクトの関係は以下の
通りです:


.. - A schema is created by a developer.  It is a collection of
..   :term:`schema node` objects.

- スキーマは開発者によって作成されます。
  それは :term:`schema node` オブジェクトのコレクションです。


.. - When a root schema node is passed to the :class:`deform.Form`
..   constructor, the result is a :term:`field` object.  For each node
..   defined by the developer in the schema recursively, a corresponding
..   :term:`field` is created.

- :class:`deform.Form` コンストラクタに root スキーマノードが渡された場合、
  その結果は :term:`field` オブジェクトになります。開発者によって再帰的に
  定義されたスキーマの各ノードについて、対応する :term:`field` が生成されます。


.. - Each field in the resulting field tree has a default widget type.
..   If the ``widget`` attribute of a field object is not set directly by
..   the developer, a property is used to create an instance of the
..   default widget type when ``field.widget`` is first requested.  Sane
..   defaults for each schema type typically exist; if a sane default
..   cannot be found, the :class:`deform.widget.TextInputWidget` widget
..   is used.

- 結果として生じるフィールド木構造の中のそれぞれのフィールドは、
  デフォルトのウィジェット型を持っています。フィールドオブジェクトの
  ``widget`` 属性が開発者によって直接セットされない場合、
  ``field.widget`` が最初に要求されたときに、デフォルトウィジェット型の
  インスタンスを生成するためにプロパティが使用されます。各スキーマ型には
  良識的なデフォルトが典型的に存在します; 良識的なデフォルトを見つける
  ことができない場合、:class:`deform.widget.TextInputWidget` ウィジェットが
  使用されます。


.. note::

   .. The `Colander documentation
   .. <http://docs.pylonsproject.org/projects/colander/dev/>`_ is a resource
   .. useful to Deform developers.  In particular, it details how a
   .. :term:`schema` is created and used.  Deform schemas are Colander schemas.
   .. The Colander documentation about how they work applies to creating Deform
   .. schemas as well.

   `Colander ドキュメンテーション
   <http://docs.pylonsproject.org/projects/colander/dev/>`_ は Deform
   開発者にとって有用なリソースです。特に :term:`schema` がどのように
   生成され、使用されるかの詳しい説明があります。Deform スキーマは
   Colander スキーマです。それらがどのように働くかについての Colander
   ドキュメンテーションは、同様に Deform スキーマを作成する場合にも
   当てはまります。


.. A widget is related to one or more :term:`schema node` type objects.
.. For example, a notional "TextInputWidget" may be responsible for
.. serializing textual data related to a schema node which has
.. :class:`colander.String` as its type into a text input control, while
.. a notional "MappingWidget" might be responsible for serializing a
.. :class:`colander.Mapping` object into a sequence of controls.  In both
.. cases, the data type being serialized is related to the schema node
.. type to which the widget is related.

ウィジェットは複数の :term:`schema node` 型オブジェクトと関係があります。
例えば、抽象的な "TextInputWidget" は、その型として
:class:`colander.String` を持つスキーマノードと関連するテキストデータを
テキスト入力コントロールにシリアライズさせることに責任を持つかもしれません。
その一方で、抽象的な "MappingWidget" は :class:`colander.Mapping`
オブジェクトをコントロールのシーケンスにシリアライズさせることに責任を
持つかもしれません。両方の場合で、シリアライズされるデータ型は、
ウィジェットが関連付けられているスキーマノード型と関係があります。


.. A widget has a relationship to a schema node via a :term:`field`
.. object.  A :term:`field` object has a reference to both a widget and a
.. :term:`schema node`.  These relationships look like this:

ウィジェットは :term:`field` オブジェクトを通してスキーマノードと関係を
持っています。 :term:`field` オブジェクトはウィジェットと :term:`schema
node` の両方への参照を持っています。これらの関係を図にすると、このように
なります:


.. ::

   field object (``field``)
        |
        |
        |----- widget object  (``field.widget``)
        |
        |
        \----- schema node object (``field.schema``)

::

   フィールド・オブジェクト (``field``)
        |
        |
        |----- ウィジェット・オブジェクト  (``field.widget``)
        |
        |
        \----- スキーマノード・オブジェクト (``field.schema``)

