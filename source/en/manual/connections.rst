***********
Connections
***********

============
Introduction
============

From the start Doctrine has been designed to work with multiple
connections. Unless separately specified Doctrine always uses the
current connection for executing the queries.

In this chapter we will demonstrate how to create and work with Doctrine
connections.

===================
Opening Connections
===================

``Doctrine_Manager`` provides the static method
``Doctrine_Manager::connection()`` which opens new connections.

In this example we will show you to open a new connection:

 // test.php

// ... $conn =
Doctrine\_Manager::connection('mysql://username:password@localhost/test',
'connection 1');

====================
Retrieve Connections
====================

If you use the ``Doctrine_Manager::connection()`` method and don't pass
any arguments it will return the current connection:

 // test.php

// ... $conn2 = Doctrine\_Manager::connection();

if ($conn === $conn2) { echo 'Doctrine\_Manager::connection() returns
the current connection'; }

==================
Current Connection
==================

The current connection is the last opened connection. In the next
example we will show how you can get the current connection from the
``Doctrine_Manager`` instance:

 // test.php

// ... $conn2 =
Doctrine\_Manager::connection('mysql://username2:password2@localhost/test2',
'connection 2');

if ($conn2 === $manager->getCurrentConnection()) { echo 'Current
connection is the connection we just created!'; }

=========================
Change Current Connection
=========================

You can change the current connection by calling
``Doctrine_Manager::setCurrentConnection()``.

 // test.php

// ... $manager->setCurrentConnection('connection 1');

echo $manager->getCurrentConnection()->getName(); // connection 1

=====================
Iterating Connections
=====================

You can iterate over the opened connections by simply passing the
manager object to a foreach clause. This is possible since
``Doctrine_Manager`` implements special ``IteratorAggregate``
interface.

.. tip::

    The ``IteratorAggregate`` is a special PHP interface for
    implementing iterators in to your objects.

// test.php

// ... foreach($manager as $conn) { echo $conn->getName() . ""; }

===================
Get Connection Name
===================

You can easily get the name of a ``Doctrine_Connection`` instance with
the following code:

 // test.php

// ... $conn = Doctrine\_Manager::connection();

$name = :code:`manager->getConnectionName(`\ conn);

echo $name; // connection 1

================
Close Connection
================

You can easily close a connection and remove it from the Doctrine
connection registry with the following code:

 // test.php

// ... $conn = Doctrine\_Manager::connection();

:code:`manager->closeConnection(`\ conn);

If you wish to close the connection but not remove it from the Doctrine
connection registry you can use the following code instead:

 // test.php

// ... $conn = Doctrine\_Manager::connection(); $conn->close();

===================
Get All Connections
===================

You can retrieve an array of all the registered connections by using the
``Doctrine_Manager::getConnections()`` method like below:

 // test.php

// ... $conns = :code:`manager->getConnections(); foreach (`\ conns as
$conn) { echo $conn->getName() . ""; }

The above is essentially the same as iterating over the
``Doctrine_Manager`` object like we did earlier. Here it is again:

 // test.php

// ... foreach ($manager as $conn) { echo $conn->getName() . ""; }

=================
Count Connections
=================

You can easily get the number of connections from a
``Doctrine_Manager`` object since it implements the ``Countable``
interface.

 // test.php

// ... :code:`num = count(`\ manager);

echo $num;

The above is the same as doing:

 // test.php

// ... $num = $manager->count();

==============================
Creating and Dropping Database
==============================

When you create connections using Doctrine, you gain the ability to
easily create and drop the databases related to those connections.

This is as simple as using some functions provided in the
``Doctrine\_Manager`` or ``Doctrine_Connection`` classes.

The following code will iterate over all instantiated connections and
call the ``dropDatabases()``/``createDatabases()`` function on each one:

 // test.php

// ... $manager->createDatabases();

$manager->dropDatabases();

**Drop/create database for specific connection**

You can easily drop or create the database for a specific
``Doctrine_Connection`` instance by calling the
``dropDatabase()``/``createDatabase()`` function on the connection
instance with the following code:

 // test.php

// ... $conn->createDatabase();

$conn->dropDatabase();

==========================
Writing Custom Connections
==========================

Sometimes you might need the ability to create your own custom
connection classes and utilize them. You may need to extend mysql, or
write your own connection type completely. This is possible by writing a
few classes and then registering the new connection type with Doctrine.

So in order to create a custom connection first we need to write the
following classes.

 class Doctrine\_Connection\_Test extends Doctrine\_Connection\_Common {
}

class Doctrine\_Adapter\_Test implements Doctrine\_Adapter\_Interface {
// ... all the methods defined in the interface }

Now we register them with Doctrine:

 // bootstrap.php

// ... $manager->registerConnectionDriver('test',
'Doctrine\_Connection\_Test');

With those few changes something like this is now possible:

 $conn =
$manager->openConnection('test://username:password@localhost/dbname');

If we were to check what classes are used for the connection you will
notice that they are the classes we defined above.

 echo
get\_class(:code:`conn); // Doctrine_Connection_Test echo get_class(`\ conn->getDbh());
// Doctrine\_Adapter\_Test

==========
Conclusion
==========

Now that we have learned all about Doctrine connections we should be
ready to dive right in to models in the [doc introduction-to-models
:name] chapter. We will learn a little bit about Doctrine models first.
Then we will start to have some fun and create our first test models and
see what kind of magic Doctrine can provide for you.
