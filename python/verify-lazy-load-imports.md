# Verify and lazy load imports of a python package

Scenario: You have a python package with an entry point class that needs to be instantiated and
setup before other classes in the package can be imported and used.

Solution: Intercept the getattr method of the package's `__init__.py` file to verify and lazy load
the other classes in the package.

`__init__.py`
```python
# serverless

# Import entry point class
from .adr import ADR

# Expose entry point class
__all__ = ["ADR"]


# Lazy import mechanism for dependent classes
class _LazyLoader:
    """
    Lazy loader for dependent classes to defer imports until accessed.
    This enforces ADR.setup() to be called before triggering the imports.
    """

    def __init__(self):
        self._initialized = False

    def _initialize(self):
        """Perform the actual imports when accessed for the first time."""
        # Ensure ADR has been set up
        ADR.ensure_setup()

        # Define the modules to be imported
        item_classes = (
            "Item",
            "HTML",
            "Animation",
            ...
        )
        # Dynamically import classes from item and template using importlib
        import importlib

        item_module = importlib.import_module(".item", package=__package__)

        # Dynamically add these to the module namespace and __all__
        for cls_name in item_classes:
            cls = getattr(item_module, cls_name, None)
            if cls:
                globals()[cls_name] = cls
                __all__.append(cls_name)

        self._initialized = True

    def __getattr__(self, name):
        if not self._initialized:
            self._initialize()
        return globals()[name]


# Create a lazy loader instance
_lazy_loader = _LazyLoader()


# Define a custom module-level __getattr__ to defer imports
def __getattr__(name):
    return getattr(_lazy_loader, name)
```

`adr.py`
```python
class ADR:
    # Class-level variable to track the active instance
    _curr_instance = None

    def __init__(
        self,
        ...
    ) -> None:
        ...
        self.is_setup = False
        ADR._curr_instance = self  # Set this as the current active instance

    def setup(self):
        """
        Perform the setup operations for the ADR instance.
        """
        ... # Perform setup operations
        self.is_setup = True

    @classmethod
    def ensure_setup(cls):
        """
        Check if the current ADR instance has been set up.
        Raise an ImportError if not.
        """
        if not cls._curr_instance or not cls._curr_instance.is_setup:
            raise ImportError(
                "ADR has not been set up yet. Please create an ADR instance and call its `setup()` method "
                "before importing and using other classes."
            )

```
