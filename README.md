# ResurrectJS

ResurrectJS preserves object behavior (prototypes) and reference
circularity with a special JSON encoding. Unlike regular JSON, Date
objects and `undefined` are also properly preserved.

## Examples

```javascript
function Foo() {}
Foo.prototype.greet = function() { return "hello"; };

// Behavior is preserved:
var necromancer = new Resurrect();
var json = necromancer.stringify(new Foo());
var foo = necromancer.resurrect(json);
foo.greet();  // => "hello"

// References to the same object are preserved:
json = necromancer.stringify([foo, foo]);
var array = necromancer.resurrect(json);
array[0] === array[1];  // => true
array[1].greet();  // => "hello"

// Dates are restored properly
json = necromancer.stringify(new Date());
var date = necromancer.resurrect(json);
Object.prototype.toString.call(date);  // => "[object Date]"
```

## Options

Options are provided to the constructor as an object with these
properties:

 * *prefix* (`"#"`): A prefix string used for temporary properties added
     to objects during serialization and deserialization. It is
     important that you don't use any properties beginning with this
     string. This option must be consistent between both serialization
     and deserialization.

 * *cleanup* (`false`): Perform full property cleanup after both
     serialization and deserialization using the delete operator. This
     may cause performance penalties (breaking hidden classes in V8)
     on objects that ResurrectJS touches, to enable with care.

 * *revive* (`true`): Restore behavior (`__proto__`) to objects that
     have been resurrected. If this is set to false during
     serialization, resurrection information will not be encoded. You
     still get circularity and Date support.

For example,

```javascript
var necromancer = new Resurrect({
    prefix: '__#',
    cleanup: true
});
```

## Restrictions

All constructors must be named and stored in the global variable under
that name. This is required so that the prototypes can be looked up
and reconnected at resurrection time.

The wrapper objects Boolean, String, and Number will be
unwrapped. This means extra properties added to these objects will not
be preserved.

## See Also

* [HydrateJS](https://github.com/nanodeath/HydrateJS)
