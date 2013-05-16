Deform
======

.. :mod:`deform` is a Python HTML form generation library.  It runs under Python
.. 2.6, 2.7, 3.2 and 3.3.

:mod:`deform` は Python の HTML フォーム生成ライブラリです。
Python 2.6, 2.7, 3.2, 3.3 で動きます。


.. The design of :mod:`deform` is heavily influenced by the `formish
.. <http://ish.io/projects/show/formish>`_ form generation library.  Some
.. might even say it's a shameless rip-off; this would not be completely
.. inaccurate.  It differs from formish mostly in ways that make the
.. implementation (arguably) simpler and smaller.

:mod:`deform` の設計は、 `formish
<http://ish.io/projects/show/formish>`_ フォーム生成ライブラリの影響を
強く受けています。恥知らずな横取りだと言う人もいるかもしれません;
これは完全に不正確というわけでもありません。 :mod:`deform` は、主に
実装が (ほぼ間違いなく) より単純で、より小さいという点で formish と異なります。


.. :mod:`deform` uses :term:`Colander` as a schema library,
.. :term:`Peppercorn` as a form control deserialization library, and
.. :term:`Chameleon` to perform HTML templating.

:mod:`deform` は、スキーマライブラリとして :term:`Colander` を、フォーム
コントロールの deserialization ライブラリとして :term:`Peppercorn` を、
HTML テンプレートとして :term:`Chameleon` を使用します。


.. :mod:`deform` depends only on Peppercorn, Colander, Chameleon and an
.. internationalization library named translationstring, so it may be
.. used in most web frameworks (or antiframeworks) as a result.

:mod:`deform` は、 Peppercorn, Colander, Chameleon と
translationstring という名前の国際化ライブラリにのみ依存しています。
したがって、ほとんどのウェブフレームワーク (あるいは反フレームワーク)
の中で使用することができるでしょう。


.. Alternate templating languages may be used, as long as all templates
.. are translated from the native Chameleon templates to your templating
.. system of choice and a suitable :term:`renderer` is supplied to
.. :mod:`deform`.

代わりのテンプレート言語を用いることもできます。そのためには、すべての
テンプレートが既存の Chameleon テンプレートからあなたが選択した
テンプレートシステムに翻訳されて、適切な :term:`renderer` が
:mod:`deform` に提供される必要があります。


.. Topics

目次
======

.. toctree::
   :maxdepth: 2

   basics.rst
   retail.rst
   common_needs.rst
   components.rst
   serialization.rst
   templates.rst
   widget.rst
   app.rst
   ajax.rst
   i18n.rst
   api.rst
   interfaces.rst
   glossary.rst
   changes.rst

.. Demonstration Site

デモサイト
==================

.. Visit `deformdemo.repoze.org <http://deformdemo.repoze.org>`_ to view an
.. application which demonstrates most of Deform's features.  The source code
.. for this application is also available in the `deform package on GitHub
.. <https://github.com/Pylons/deform>`_.

`deformdemo.repoze.org <http://deformdemo.repoze.org>`_ を訪れて、
Deform のほとんどの特徴をデモするアプリケーションを見てください。
また、このアプリケーションのソースコードは `GitHub の deform パッケージ
<https://github.com/Pylons/deform>`_ の中にあります。


.. Support and Development

サポートと開発
=======================

.. To report bugs, use the `bug tracker
.. <https://github.com/Pylons/deform/issues>`_.

バグを報告するためには、 `バグトラッカー
<https://github.com/Pylons/deform/issues>`_ を使用してください。


.. If you've got questions that aren't answered by this documentation, contact
.. the `Pylons-discuss maillist
.. <http://groups.google.com/group/pylons-discuss>`_ or join the 

このドキュメンテーションに答えが見つからない質問がある場合、
`Pylons-discuss メーリングリスト
<http://groups.google.com/group/pylons-discuss>`_ にコンタクトを取るか、


.. only:: not latex

  .. `#pylons IRC channel <irc://irc.freenode.net/#pylons>`_.

  `#pylons IRC チャンネル <irc://irc.freenode.net/#pylons>`_ に参加してください。


.. only:: latex
    
  .. #pylons IRC channel ``irc://irc.freenode.net/#pylons``.

  #pylons IRC チャンネル ``irc://irc.freenode.net/#pylons`` に参加してください。


.. Browse and check out tagged and trunk versions of :mod:`deform` via the
.. `deform package on GitHub <https://github.com/Pylons/deform>`_.  To check out
.. the trunk, use this command:

:mod:`deform` のタグまたはトランクバージョンは、 `GitHub 上の deform
パッケージ <https://github.com/Pylons/deform>`_ を通して閲覧または
チェックアウトできます。トランクをチェックアウトするには以下のコマンドを
使用してください:


::

   git clone git://github.com/Pylons/deform.git


.. To find out how to become a contributor to :mod:`deform`, please see the
.. `Pylons Project contributor documentation
.. <http://docs.pylonsproject.org/#contributing/>`_.

:mod:`deform` のコントリビューターになる方法を知るには、 `Pylons
プロジェクトのコントリビュータードキュメンテーション
<http://docs.pylonsproject.org/#contributing/>`_ を参照してください。


.. Index and Glossary

索引と用語集
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`

.. Thanks

謝辞
======

.. Without these people, this software would not exist:

これらの人々なくしては、このソフトウェアは存在しませんでした:


.. - The Formish guys (http://ish.io)

- Formish の人たち (http://ish.io)

- Tres Seaver

- Fear Factory (http://fearfactory.com)

- Midlake (http://midlake.net)

