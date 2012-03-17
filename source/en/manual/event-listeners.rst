***************
Event Listeners
***************

============
Introduction
============

Doctrine provides flexible event listener architecture that not only
allows listening for different events but also for altering the
execution of the listened methods.

There are several different listeners and hooks for various Doctrine
components. Listeners are separate classes whereas hooks are empty
template methods within the listened class.

Hooks are simpler than event listeners but they lack the separation of
different aspects. An example of using ``Doctrine_Record`` hooks:

 // models/BlogPost.php

class BlogPost extends Doctrine\_Record { // ...

::

    public function preInsert($event)
    {
        $invoker = $event->getInvoker();

        $invoker->created = date('Y-m-d', time());
    }

}

.. note::

    By now we have defined lots of models so you should be able
    to define your own ``setTableDefinition()`` for the ``BlogPost``
    model or even create your own custom model!

Now you can use the above model with the following code assuming we
added a ``title``, ``body`` and ``created`` column to the model:

 // test.php

// ... $blog = new BlogPost(); $blog->title = 'New title'; $blog->body =
'Some content'; $blog->save();

echo $blog->created;

The above example will output the current date as PHP knows it.

Each listener and hook method takes one parameter ``Doctrine_Event``
object. ``Doctrine_Event`` object holds information about the event in
question and can alter the execution of the listened method.

For the purposes of this documentation many method tables are provided
with column named ``params`` indicating names of the parameters that an
event object holds on given event. For example the
``preCreateSavepoint`` event has one parameter with the name of the
created ``savepoint``, which is quite intuitively named as
``savepoint``.

====================
Connection Listeners
====================

Connection listeners are used for listening the methods of
``Doctrine_Connection`` and its modules (such as
``Doctrine_Transaction``). All listener methods take one argument
``Doctrine_Event`` which holds information about the listened event.

-----------------------
Creating a New Listener
-----------------------

There are three different ways of defining a listener. First you can
create a listener by making a class that inherits
``Doctrine_EventListener``:

 class MyListener extends Doctrine\_EventListener { public function
preExec(Doctrine\_Event $event) {

::

    }

}

Note that by declaring a class that extends ``Doctrine_EventListener``
you don't have to define all the methods within the
``Doctrine\_EventListener_Interface``. This is due to a fact that
``Doctrine_EventListener`` already has empty skeletons for all these
methods.

Sometimes it may not be possible to define a listener that extends
``Doctrine_EventListener`` (you might have a listener that inherits
some other base class). In this case you can make it implement
``Doctrine\_EventListener_Interface``.

 class MyListener implements Doctrine\_EventListener\_Interface { public
function preTransactionCommit(Doctrine\_Event $event) {} public function
postTransactionCommit(Doctrine\_Event $event) {}

::

    public function preTransactionRollback(Doctrine_Event $event) {}
    public function postTransactionRollback(Doctrine_Event $event) {}

    public function preTransactionBegin(Doctrine_Event $event) {}
    public function postTransactionBegin(Doctrine_Event $event) {}

    public function postConnect(Doctrine_Event $event) {}
    public function preConnect(Doctrine_Event $event) {}

    public function preQuery(Doctrine_Event $event) {}
    public function postQuery(Doctrine_Event $event) {}

    public function prePrepare(Doctrine_Event $event) {}
    public function postPrepare(Doctrine_Event $event) {}

    public function preExec(Doctrine_Event $event) {}
    public function postExec(Doctrine_Event $event) {}

    public function preError(Doctrine_Event $event) {}
    public function postError(Doctrine_Event $event) {}

    public function preFetch(Doctrine_Event $event) {}
    public function postFetch(Doctrine_Event $event) {}

    public function preFetchAll(Doctrine_Event $event) {}
    public function postFetchAll(Doctrine_Event $event) {}

    public function preStmtExecute(Doctrine_Event $event) {}
    public function postStmtExecute(Doctrine_Event $event) {}

}

.. caution::

    All listener methods must be defined here otherwise PHP
    throws fatal error.

The third way of creating a listener is a very elegant one. You can make
a class that implements ``Doctrine_Overloadable``. This interface has
only one method: ``\__call()``, which can be used for catching *all*
the events.

 class MyDebugger implements Doctrine\_Overloadable { public function
\_\_call($methodName, $args) { echo $methodName . ' called !'; } }

-------------------
Attaching listeners
-------------------

You can attach the listeners to a connection with setListener().

 $conn->setListener(new MyDebugger());

If you need to use multiple listeners you can use addListener().

 $conn->addListener(new MyDebugger()); $conn->addListener(new
MyLogger());

--------------------
Pre and Post Connect
--------------------

All of the below listeners are invoked in the ``Doctrine_Connection``
class. And they are all passed an instance of ``Doctrine_Event``.

\|\|~ Methods \|\|~ Listens \|\|~ Params \|\| \|\| ``preConnect()`` \|\|
``connection()`` \|\| \|\| \|\| ``postConnect()`` \|\| ``connection()``
\|\| \|\|

---------------------
Transaction Listeners
---------------------

All of the below listeners are invoked in the ``Doctrine_Transaction``
class. And they are all passed an instance of ``Doctrine_Event``.

\|\|~ Methods \|\|~ Listens \|\|~ Params \|\| \|\|
``preTransactionBegin()`` \|\| ``beginTransaction()`` \|\| \|\| \|\|
``postTransactionBegin()`` \|\| ``beginTransaction()`` \|\| \|\| \|\|
``preTransactionRollback()`` \|\| ``rollback()`` \|\| \|\| \|\|
``postTransactionRollback()`` \|\| ``rollback()`` \|\| \|\| \|\|
``preTransactionCommit()`` \|\| ``commit()`` \|\| \|\| \|\|
``postTransactionCommit()`` \|\| ``commit()`` \|\| \|\| \|\|
``preCreateSavepoint()`` \|\| ``createSavepoint()`` \|\| ``savepoint``
\|\| \|\| ``postCreateSavepoint()`` \|\| ``createSavepoint()`` \|\|
``savepoint`` \|\| \|\| ``preRollbackSavepoint()`` \|\|
``rollbackSavepoint()`` \|\| ``savepoint`` \|\| \|\|
``postRollbackSavepoint()`` \|\| ``rollbackSavepoint()`` \|\|
``savepoint`` \|\| \|\| ``preReleaseSavepoint()`` \|\|
``releaseSavepoint()`` \|\| ``savepoint`` \|\| \|\|
``postReleaseSavepoint()`` \|\| ``releaseSavepoint()`` \|\|
``savepoint`` \|\|

 class MyTransactionListener extends Doctrine\_EventListener { public
function preTransactionBegin(Doctrine\_Event $event) { echo 'beginning
transaction... '; }

::

    public function preTransactionRollback(Doctrine_Event $event)
    {
        echo 'rolling back transaction... ';
    }

}

-------------------------
Query Execution Listeners
-------------------------

All of the below listeners are invoked in the ``Doctrine_Connection``
and ``Doctrine\_Connection_Statement`` classes. And they are all passed
an instance of ``Doctrine_Event``.

\|\|~ Methods \|\|~ Listens \|\|~ Params \|\| \|\| ``prePrepare()`` \|\|
``prepare()`` \|\| ``query`` \|\| \|\| ``postPrepare()`` \|\|
``prepare()`` \|\| ``query`` \|\| \|\| ``preExec()`` \|\| ``exec()``
\|\| ``query`` \|\| \|\| ``postExec()`` \|\| ``exec()`` \|\| ``query,
rows`` \|\| \|\| ``preStmtExecute()`` \|\| ``execute()`` \|\| ``query``
\|\| \|\| ``postStmtExecute()`` \|\| ``execute()`` \|\| ``query`` \|\|
\|\| ``preExecute()`` \|\| ``execute()`` \* \|\| ``query`` \|\| \|\|
``postExecute()`` \|\| ``execute()`` \* \|\| ``query`` \|\| \|\|
``preFetch()`` \|\| ``fetch()`` \|\| ``query, data`` \|\| \|\|
``postFetch()`` \|\| ``fetch()`` \|\| ``query, data`` \|\| \|\|
``preFetchAll()`` \|\| ``fetchAll()`` \|\| ``query, data`` \|\| \|\|
``postFetchAll()`` \|\| ``fetchAll()`` \|\| ``query, data`` \|\|

.. note::

    ``preExecute()`` and ``postExecute()`` only get invoked
    when ``Doctrine_Connection::execute()`` is being called without
    prepared statement parameters. Otherwise
    ``Doctrine_Connection::execute()`` invokes ``prePrepare()``,
    ``postPrepare()``, ``preStmtExecute()`` and ``postStmtExecute()``.

===================
Hydration Listeners
===================

The hydration listeners can be used for listening to resultset hydration
procedures. Two methods exist for listening to the hydration procedure:
``preHydrate()`` and ``postHydrate()``.

If you set the hydration listener on connection level the code within
the ``preHydrate()`` and ``postHydrate()`` blocks will be invoked by all
components within a multi-component resultset. However if you add a
similar listener on table level it only gets invoked when the data of
that table is being hydrated.

Consider we have a class called ``User`` with the following fields:
``first\_name``, ``last_name`` and ``age``. In the following example we
create a listener that always builds a generated field called
``full\_name`` based on ``first\_name`` and ``last_name`` fields.

 // test.php

// ... class HydrationListener extends Doctrine\_Record\_Listener {
public function preHydrate(Doctrine\_Event $event) { $data =
$event->data;

::

        $data['full_name'] = $data['first_name'] . ' ' . $data['last_name'];
        $event->data = $data;
    }

}

Now all we need to do is attach this listener to the ``User`` record and
fetch some users:

 // test.php

// ... $userTable = Doctrine\_Core::getTable('User');
$userTable->addRecordListener(new HydrationListener());

$q = Doctrine\_Query::create() ->from('User');

$users = $q->execute();

foreach ($users as $user) { echo $user->full\_name; }

================
Record Listeners
================

``Doctrine_Record`` provides listeners very similar to
``Doctrine_Connection``. You can set the listeners at global,
connection and table level.

Here is a list of all available listener methods:

All of the below listeners are invoked in the ``Doctrine_Record`` and
``Doctrine_Validator`` classes. And they are all passed an instance of
``Doctrine_Event``.

\|\|~ Methods \|\|~ Listens \|\| \|\| ``preSave()`` \|\| ``save()`` \|\|
\|\| ``postSave()`` \|\| ``save()`` \|\| \|\| ``preUpdate()`` \|\|
``save()`` when the record state is ``DIRTY`` \|\| \|\| ``postUpdate()``
\|\| ``save()`` when the record state is ``DIRTY`` \|\| \|\|
``preInsert()`` \|\| ``save()`` when the record state is ``TDIRTY`` \|\|
\|\| ``postInsert()`` \|\| ``save()`` when the record state is
``TDIRTY`` \|\| \|\| ``preDelete()`` \|\| ``delete()`` \|\| \|\|
``postDelete()`` \|\| ``delete()`` \|\| \|\| ``preValidate()`` \|\|
``validate()`` \|\| \|\| ``postValidate()`` \|\| ``validate()`` \|\|

Just like with connection listeners there are three ways of defining a
record listener: by extending ``Doctrine\_Record_Listener``, by
implementing ``Doctrine\_Record\_Listener_Interface`` or by
implementing ``Doctrine_Overloadable``.

In the following we'll create a global level listener by implementing
``Doctrine_Overloadable``:

 class Logger implements Doctrine\_Overloadable { public function
\_\_call($m, $a) { echo 'caught event ' . $m;

::

        // do some logging here...
    }

}

Attaching the listener to manager is easy:

 $manager->addRecordListener(new Logger());

Note that by adding a manager level listener it affects on all
connections and all tables / records within these connections. In the
following we create a connection level listener:

 class Debugger extends Doctrine\_Record\_Listener { public function
preInsert(Doctrine\_Event $event) { echo 'inserting a record ...'; }

::

    public function preUpdate(Doctrine_Event $event)
    {
        echo 'updating a record...';
    }

}

Attaching the listener to a connection is as easy as:

 $conn->addRecordListener(new Debugger());

Many times you want the listeners to be table specific so that they only
apply on the actions on that given table.

Here is an example:

 class Debugger extends Doctrine\_Record\_Listener { public function
postDelete(Doctrine\_Event $event) { echo 'deleted ' .
$event->getInvoker()->id; } }

Attaching this listener to given table can be done as follows:

 class MyRecord extends Doctrine\_Record { // ...

::

    public function setUp()
    {
        $this->addListener(new Debugger());
    }

}

============
Record Hooks
============

All of the below listeners are invoked in the ``Doctrine_Record`` and
``Doctrine_Validator`` classes. And they are all passed an instance of
``Doctrine_Event``.

\|\|~ Methods \|\|~ Listens \|\| \|\| ``preSave()`` \|\| ``save()`` \|\|
\|\| ``postSave()`` \|\| ``save()`` \|\| \|\| ``preUpdate()`` \|\|
``save()`` when the record state is ``DIRTY`` \|\| \|\| ``postUpdate()``
\|\| ``save()`` when the record state is ``DIRTY`` \|\| \|\|
``preInsert()`` \|\| ``save()`` when the record state is ``TDIRTY`` \|\|
\|\| ``postInsert()`` \|\| ``save()`` when the record state is
``TDIRTY`` \|\| \|\| ``preDelete()`` \|\| ``delete()`` \|\| \|\|
``postDelete()`` \|\| ``delete()`` \|\| \|\| ``preValidate()`` \|\|
``validate()`` \|\| \|\| ``postValidate()`` \|\| ``validate()`` \|\|

Here is a simple example where we make use of the ``preInsert()`` and
``preUpdate()`` methods:

 class BlogPost extends Doctrine\_Record { public function
setTableDefinition() { $this->hasColumn('title', 'string', 200);
$this->hasColumn('content', 'string'); $this->hasColumn('created',
'date'); $this->hasColumn('updated', 'date'); }

::

    public function preInsert($event)
    {
        $this->created = date('Y-m-d', time());
    }

    public function preUpdate($event)
    {
        $this->updated = date('Y-m-d', time());
    }

}

=========
DQL Hooks
=========

Doctrine allows you to attach record listeners globally, on each
connection, or on specific record instances. ``Doctrine_Query``
implements ``preDql\*()`` hooks which are checked for on any attached
record listeners and checked for on the model instance itself whenever a
query is executed. The query will check all models involved in the
``from`` part of the query for any hooks which can alter the query that
invoked the hook.

Here is a list of the hooks you can use with DQL:

\|\|~ Methods \|\|~ Listens \|\| \|\| ``preDqlSelect()`` \|\| ``from()``
\|\| \|\| ``preDqlUpdate()`` \|\| ``update()`` \|\| \|\|
``preDqlDelete()`` \|\| ``delete()`` \|\|

Below is an example record listener attached directly to the model which
will implement the ``SoftDelete`` functionality for the ``User`` model.

.. tip::

    The SoftDelete functionality is included in Doctrine as a
    behavior. This code is used to demonstrate how to use the select,
    delete, and update DQL listeners to modify executed queries. You can
    use the SoftDelete behavior by specifying
    ``$this->actAs('SoftDelete')`` in your ``Doctrine_Record::setUp()``
    definition.

 class UserListener extends Doctrine\_EventListener { /\*\* \* Skip the
normal delete options so we can override it with our own \* \* @param
Doctrine\_Event $event \* @return void \*/ public function
preDelete(Doctrine\_Event $event) { $event->skipOperation(); }

::

    /**
     * Implement postDelete() hook and set the deleted flag to true
     *
     * @param Doctrine_Event $event
     * @return void
     */
    public function postDelete(Doctrine_Event $event)
    {
        $name = $this->_options['name'];
        $event->getInvoker()->$name = true;
        $event->getInvoker()->save();
    }

    /**
     * Implement preDqlDelete() hook and modify a dql delete query so it updates the deleted flag
     * instead of deleting the record
     *
     * @param Doctrine_Event $event
     * @return void
     */
    public function preDqlDelete(Doctrine_Event $event)
    {
        $params = $event->getParams();
        $field = $params['alias'] . '.deleted';
        $q = $event->getQuery();
        if ( ! $q->contains($field)) {
            $q->from('')->update($params['component'] . ' ' . $params['alias']);
            $q->set($field, '?', array(false));
            $q->addWhere($field . ' = ?', array(true));
        }
    }

    /**
     * Implement preDqlDelete() hook and add the deleted flag to all queries for which this model 
     * is being used in.
     *
     * @param Doctrine_Event $event 
     * @return void
     */
    public function preDqlSelect(Doctrine_Event $event)
    {
        $params = $event->getParams();
        $field = $params['alias'] . '.deleted';
        $q = $event->getQuery();
        if ( ! $q->contains($field)) {
            $q->addWhere($field . ' = ?', array(false));
        }
    }

}

All of the above methods in the listener could optionally be placed in
the user class below. Doctrine will check there for the hooks as well:

 class User extends Doctrine\_Record { // ...

::

    public function preDqlSelect()
    {
        // ...
    }

    public function preDqlUpdate()
    {
        // ...
    }

    public function preDqlDelete()
    {
        // ...
    }

}

In order for these dql callbacks to be checked, you must explicitly turn
them on. Because this adds a small amount of overhead for each query, we
have it off by default. We already enabled this attribute in an earlier
chapter.

Here it is again to refresh your memory:

 // bootstrap.php

// ... $manager->setAttribute(Doctrine\_Core::ATTR\_USE\_DQL\_CALLBACKS,
true);

Now when you interact with the User model it will take in to account the
deleted flag:

Delete user through record instance:

 $user = new User(); $user->username = 'jwage'; $user->password =
'changeme'; $user->save(); $user->delete();

.. note::

    The above call to ``$user->delete()`` does not actually
    delete the record instead it sets the deleted flag to true.

 $q = Doctrine\_Query::create() ->from('User u');

echo $q->getSqlQuery();

 SELECT u.id AS u**id, u.username AS u**username, u.password AS
u**password, u.deleted AS u**deleted FROM user u WHERE u.deleted = ?

.. note::

    Notice how the ``"u.deleted = ?"`` was automatically added
    to the where condition with a parameter value of //true//.

==================
Chaining Listeners
==================

Doctrine allows chaining of different event listeners. This means that
more than one listener can be attached for listening the same events.
The following example attaches two listeners for given connection:

In this example ``Debugger`` and ``Logger`` both inherit
``Doctrine_EventListener``:

 $conn->addListener(new Debugger()); $conn->addListener(new Logger());

================
The Event object
================

-------------------
Getting the Invoker
-------------------

You can get the object that invoked the event by calling
``getInvoker()``:

 class MyListener extends Doctrine\_EventListener { public function
preExec(Doctrine\_Event $event) { $event->getInvoker(); //
Doctrine\_Connection } }

-----------
Event Codes
-----------

``Doctrine_Event`` uses constants as event codes. Below is the list of
all available event constants:

-  ``Doctrine\_Event::CONN_QUERY``
-  ``Doctrine\_Event::CONN_EXEC``
-  ``Doctrine\_Event::CONN_PREPARE``
-  ``Doctrine\_Event::CONN_CONNECT``
-  ``Doctrine\_Event::STMT_EXECUTE``
-  ``Doctrine\_Event::STMT_FETCH``
-  ``Doctrine\_Event::STMT_FETCHALL``
-  ``Doctrine\_Event::TX_BEGIN``
-  ``Doctrine\_Event::TX_COMMIT``
-  ``Doctrine\_Event::TX_ROLLBACK``
-  ``Doctrine\_Event::SAVEPOINT_CREATE``
-  ``Doctrine\_Event::SAVEPOINT_ROLLBACK``
-  ``Doctrine\_Event::SAVEPOINT_COMMIT``
-  ``Doctrine\_Event::RECORD_DELETE``
-  ``Doctrine\_Event::RECORD_SAVE``
-  ``Doctrine\_Event::RECORD_UPDATE``
-  ``Doctrine\_Event::RECORD_INSERT``
-  ``Doctrine\_Event::RECORD_SERIALIZE``
-  ``Doctrine\_Event::RECORD_UNSERIALIZE``
-  ``Doctrine\_Event::RECORD\_DQL_SELECT``
-  ``Doctrine\_Event::RECORD\_DQL_DELETE``
-  ``Doctrine\_Event::RECORD\_DQL_UPDATE``

Here are some examples of hooks being used and the code that is
returned:

 class MyListener extends Doctrine\_EventListener { public function
preExec(Doctrine\_Event $event) { $event->getCode(); //
Doctrine\_Event::CONN\_EXEC } }

class MyRecord extends Doctrine\_Record { public function
preUpdate(Doctrine\_Event $event) { $event->getCode(); //
Doctrine\_Event::RECORD\_UPDATE } }

-------------------
Getting the Invoker
-------------------

The method ``getInvoker()`` returns the object that invoked the given
event. For example for event ``Doctrine\_Event::CONN_QUERY`` the
invoker is a ``Doctrine_Connection`` object.

Here is an example of using the record hook named ``preUpdate()`` that
is invoked when a ``Doctrine_Record`` instance is saved and an update
is issued to the database:

 class MyRecord extends Doctrine\_Record { public function
preUpdate(Doctrine\_Event $event) { $event->getInvoker(); //
Object(MyRecord) } }

-------------------
Skip Next Operation
-------------------

``Doctrine_Event`` provides many methods for altering the execution of
the listened method as well as for altering the behavior of the listener
chain.

For some reason you may want to skip the execution of the listened
method. It can be done as follows (note that ``preExec()`` could be any
listener method):

 class MyListener extends Doctrine\_EventListener { public function
preExec(Doctrine\_Event $event) { // some business logic, then:

::

        $event->skipOperation();
    }

}

-----------------
Skip Next Listener
------------------

When using a chain of listeners you might want to skip the execution of
the next listener. It can be achieved as follows:

 class MyListener extends Doctrine\_EventListener { public function
preExec(Doctrine\_Event $event) { // some business logic, then:

::

        $event->skipNextListener();
    }

}

==========
Conclusion
==========

Event listeners are a great feature in Doctrine and combined with [doc
behaviors :name] they can provide some very complex functionality with a
minimal amount of code.

Now we are ready to move on to discuss the best feature in Doctrine for
improving performance, [doc caching :name].
