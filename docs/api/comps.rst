Objects & Fields
================

The ``esapp.components`` module provides the base classes for defining grid component schemas
and transient stability field constants.

GObject Base Class
------------------

.. automodule:: esapp.components.gobject
   :members:

Transient Stability Fields
--------------------------

The ``TS`` class provides IDE intellisense for transient stability result fields, organized
by object type. Use these constants with ``TSWatch.watch()`` to specify which fields to record
during simulation.

.. code-block:: python

    from esapp import TS
    from esapp.components import Gen, Bus
    from esapp.utils import TSWatch

    tsw = TSWatch()

    # Watch generator fields
    tsw.watch(Gen, [TS.Gen.P, TS.Gen.W, TS.Gen.Delta])

    # Watch bus fields
    tsw.watch(Bus, [TS.Bus.VPU, TS.Bus.Freq])

TS Field Reference
------------------

The following tables list all available transient stability field constants by category.
Access fields using ``TS.<Category>.<Field>`` syntax (e.g., ``TS.Gen.P``, ``TS.Bus.VPU``).

.. ts-field-list::

Finding a Field Name
--------------------

Fields use PowerWorld's *variable name*, and PowerWorld writes "Mvar" as ``MVR`` in variable
names. The generator's present Mvar output is ``Gen.GenMVR`` (concise name ``Mvar``), not
``GenMvar``:

.. code-block:: python

    from esapp.components import Gen

    gens = pw[Gen, [Gen.GenMW, Gen.GenMVR, Gen.GenStatus]]

    # List a component's fields whose name mentions Mvar
    [f for f in Gen.fields() if "MVR" in f.upper()]
    # ['GenMVRMax', 'GenMVRMin', 'GenMvrSetPoint', 'GenMVR', 'GenMVRPercent', ...]

Searching PowerWorld's "Case Object Fields" export for ``GenMvar`` is misleading: ``GenMvar`` is
the *concise* name of aggregate fields on Area, Zone, Bus, Substation and about 20 other object
types (for example ``Area.BGGenMVR``, the sum of generator Mvars in the area). On the ``Gen``
object the concise name is just ``Mvar``. Filter to the object type first, then match the concise
name or dialog path (``Mvar Output\Mvar``). Typing ``Gen.`` in an IDE does the same: autocomplete
lists only that object's fields, and the table under `Available Grid Object Types`_ shows each
field's description.

Some commonly used fields:

.. list-table::
   :widths: 12 40 48
   :header-rows: 1

   * - Object
     - Quantity
     - Field
   * - ``Gen``
     - MW / Mvar output
     - ``GenMW`` / ``GenMVR``
   * - ``Gen``
     - MW / Mvar setpoint
     - ``GenMWSetPoint`` / ``GenMvrSetPoint``
   * - ``Gen``
     - Mvar limits
     - ``GenMVRMax`` / ``GenMVRMin``
   * - ``Gen``
     - Status (Open or Closed)
     - ``GenStatus``
   * - ``Load``
     - MW / Mvar
     - ``LoadMW`` / ``LoadMVR``
   * - ``Bus``
     - Voltage (pu / kV / degrees)
     - ``BusPUVolt`` / ``BusKVVolt`` / ``BusAngle``
   * - ``Branch``
     - MW / Mvar at the from bus
     - ``LineMW`` / ``LineMVR``
   * - ``Shunt``
     - Nominal / actual Mvar
     - ``SSNMVR`` / ``SSAMVR``

``GenMW`` and ``GenMVR`` read 0.0 for a generator that is open. ``GenMWSetPoint`` and
``GenMvrSetPoint`` equal the output fields while the generator is in service and keep the setpoint
when it is open, so sum the setpoint fields to include open units. ``GenMVR`` is not settable
(``Gen.is_settable("GenMVR")`` is ``False``); change ``GenVoltSet`` to move a generator's Mvar.

Available Grid Object Types
----------------------------

The following component types are available in ``esapp.components``.
Each class represents a PowerWorld object type that can be used with
the :class:`~esapp.PowerWorld` indexing syntax (e.g., ``pw[Bus, "BusNum"]``).

.. grid-component-list::
