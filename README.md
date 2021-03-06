# PydMagic
Ipython/Jupyter magic for inline D code in a python notebook

Liable to change. Depends on pyd (https://github.com/ariovistus/pyd), mergedict (https://pypi.python.org/pypi/mergedict) and dub (http://code.dlang.org)

To install, just enter
```
%install_ext https://raw.githubusercontent.com/DlangScience/PydMagic/master/pyd_magic.py
```
in any ipython instance.

To use, first enter
```
%load_ext pyd_magic
```

then write your pyd extension in a cell marked with ```%%pyd``` e.g.

```D
%%pyd

@pdef!() string hello() {
    return "Hello World!";
}

@pdef!(Docstring!"takes a single int, returns that int converted to a string")
string intToStr(int b)
{
    import std.conv;
    return b.to!string;
}

@pdef!(PyName!"binary_zebra") int zebra()
{
    return 101010101;
}

@pdef!() long[] whereExactlyIntegral(float[] data)
{
    import std.algorithm, std.array;
    return data.filter!(x => x == cast(long)x).map!(x => cast(long)x).array;
}

@pdef!()
struct S
{
    @pdef!() string bleep = "bloop7";
    @pdef!() int bar(int w) { return w * 42; }
}
```
and run. After it has finished building, your extension should be automatically imported and ready to use. Builds are cached just like with cython magic. Some basic flags are supported as arguments to ```%%pyd``` such as ```--dub_args```, run ```%%pyd?``` for more info.

The ```pdef``` UDA instructs the extension to wrap the function/type/member for use in python. It takes any template arguments that pyd.pyd.def takes.

### dub integration
Adding a dependency on a dub package or any other part of a ```dub.json``` is as easy as ```%%pyd --dub_config={"dependencies":{"someDubPackage":"~>1.2.5"}}``` and command line arguments to dub can similarly by added with ```--dub_args```. See ```%%pyd?``` for more info.

PydMagic provides its own PydMain, so you can't define your own. You can, however, define 2 functions ```preInit()``` and ```postInit()```, which PydMagic will call before and after calling pyd's ```module_init()``` respectively. You can use this to manually wrap functions, types, anything else pyd supports.

I have tested on OS X and linux. Everything works fine on linux, but on OS X you can't import more than one extension per python instance (including modifications of the same extension) or python will crash. This is due to shared libraries not being supported on OS X. Windows is almost but not quite working.

This code is heavily based on the ```%%cython``` magic implementation.
