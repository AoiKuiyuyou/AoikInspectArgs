# AoikInspectArgs
Inspect function arguments.

Tested working with:
- Python 2.7 and 3.5

## Table of Contents
- [Setup](#setup)
  - [Setup via pip](#setup-via-pip)
  - [Setup via git](#setup-via-git)
- [Usage](#usage)
  - [Use inspect_arguments](#use-inspect_arguments)
  - [Use format_arguments](#use-format_arguments)
  - [Use inspect info filter function](#use-inspect-info-filter-function)
  - [Understand InspectInfo](#understand-inspectinfo)
  - [Understand ArgumentInfo](#understand-argumentinfo)
  - [Understand ArgumentType](#understand-argumenttype)
  - [Understand ParameterType](#understand-parametertype)

## Setup
- [Setup via pip](#setup-via-pip)
- [Setup via git](#setup-via-git)

### Setup via pip
Run:
```
pip install git+https://github.com/AoiKuiyuyou/AoikInspectArgs
```

### Setup via git
Run:
```
git clone https://github.com/AoiKuiyuyou/AoikInspectArgs

cd AoikInspectArgs

python setup.py install
```

## Usage
- [Use inspect_arguments](#use-inspect_arguments)
- [Use format_arguments](#use-format_arguments)
- [Use inspect info filter function](#use-inspect-info-filter-function)
- [Understand InspectInfo](#understand-inspectinfo)
- [Understand ArgumentInfo](#understand-argumentinfo)
- [Understand ArgumentType](#understand-argumenttype)
- [Understand ParameterType](#understand-parametertype)

### Use inspect_arguments
Code:
```
from aoikinspectargs import inspect_arguments
from pprint import pformat


# Create function
def func(a, b, c=3, *args, **kwargs):
    pass


# Positional arguments
args = [1]

# Keyword arguments
kwargs = dict(
    b=2,
    x=100,
    y=200,
    z=300
)

# Inspect function arguments
inspect_info = inspect_arguments(
    func=func,
    args=args,
    kwargs=kwargs,
)

# Print inspect info
print(pformat(inspect_info, indent=4))
```

Output:
```
{   'dupkwd_arg_infos': {},
    'fixed_arg_infos': OrderedDict([   (   'a',
                                           {   'name': 'a',
                                               'param_type': POSITIONAL_OR_KEYWORD,
                                               'pos_index': 0,
                                               'type': POSITIONAL,
                                               'value': 1,
                                               'varpos_index': None}),
                                       (   'b',
                                           {   'name': 'b',
                                               'param_type': POSITIONAL_OR_KEYWORD,
                                               'pos_index': None,
                                               'type': KEYWORD,
                                               'value': 2,
                                               'varpos_index': None}),
                                       (   'c',
                                           {   'name': 'c',
                                               'param_type': POSITIONAL_OR_KEYWORD,
                                               'pos_index': None,
                                               'type': DEFAULT,
                                               'value': 3,
                                               'varpos_index': None})]),
    'kwdonly_param_names': [],
    'missing_arg_infos': OrderedDict(),
    'poskwd_param_names': ['a', 'b', 'c'],
    'varkwd_arg_infos': {   'x': {   'name': 'x',
                                     'param_type': VAR_KEYWORD,
                                     'pos_index': None,
                                     'type': VAR_KEYWORD,
                                     'value': 100,
                                     'varpos_index': None},
                            'y': {   'name': 'y',
                                     'param_type': VAR_KEYWORD,
                                     'pos_index': None,
                                     'type': VAR_KEYWORD,
                                     'value': 200,
                                     'varpos_index': None},
                            'z': {   'name': 'z',
                                     'param_type': VAR_KEYWORD,
                                     'pos_index': None,
                                     'type': VAR_KEYWORD,
                                     'value': 300,
                                     'varpos_index': None}},
    'varkwd_param_name': 'kwargs',
    'varpos_arg_infos': [],
    'varpos_param_name': 'args'}
```

### Use format_arguments
Code:
```
from aoikinspectargs import format_arguments


# Create function
def func(a, b, c, *args, **kwargs):
    pass


# Positional arguments
args = [1, 2, 3, 10, 20, 30]

# Keyword arguments
kwargs = dict(
    x=100,
    y=200,
    z=300
)

# Format function arguments
text = format_arguments(
    func=func,
    args=args,
    kwargs=kwargs,
    repr_func=repr,
)

# Print result text
print(text)
```

Output:
```
a=1, b=2, c=3, *args=[10, 20, 30], **kwargs={x=100, y=200, z=300}
```

### Use inspect info filter function
Code:
```
from aoikinspectargs import format_arguments
from aoikinspectargs import InspectInfo


# Create class
class CustomClass(object):

    # Create function with the first parameter name being `self`
    def func(self, a):
        pass

# Positional arguments
args = [0, 1]

# Keyword arguments
kwargs = {}

# Format function arguments
text = format_arguments(
    func=CustomClass.func,
    args=args,
    kwargs=kwargs,
    repr_func=repr,
)

# Check result text
assert text == 'self=0, a=1'

# Create inspect info filter function that removes `self` argument info
def filter_func(info):
    """
    Inspect info filter function that removes `self` argument info.

    :param info: InspectInfo instance.

    :return: None.
    """
    # Get fixed argument infos dict
    fixed_arg_infos = info[InspectInfo.K_FIXED_ARG_INFOS]

    # If fixed argument infos dict is not empty
    if fixed_arg_infos:
        # Get the first fixed argument name
        first_arg_name = next(iter(fixed_arg_infos))

        # If the first fixed argument name is `self`
        if first_arg_name == 'self':
            # Remove `self` argument info
            del fixed_arg_infos['self']

# Format function arguments
text = format_arguments(
    func=CustomClass.func,
    args=args,
    kwargs=kwargs,
    repr_func=repr,
    filter_func=filter_func,
)

# Check result text
assert text == 'a=1'
```

### Understand InspectInfo
Code:
```
from aoikinspectargs import InspectInfo


# ----- InspectInfo -----
# InspectInfo instances are AttrDict instances.
# Below is the meaning of InspectInfo dict keys (accessible as attributes).

# ---- Positional-or-keyword parameter names ----
# The item value is list of strings.
#
assert InspectInfo.K_POSKWD_PARAM_NAMES == 'poskwd_param_names'

# ---- Keyword-only parameter names ----
# The item value is list of strings.
#
assert InspectInfo.K_KWDONLY_PARAM_NAMES == 'kwdonly_param_names'

# ---- Variable-positional parameter name ----
# E.g. `args` for `*args`.
# The item value is string.
#
assert InspectInfo.K_VARPOS_PARAM_NAME == 'varpos_param_name'

# ---- Variable-keyword parameter name ----
# E.g. `kwargs` for `**kwargs`.
# The item value is string.
#
assert InspectInfo.K_VARKWD_PARAM_NAME == 'varkwd_param_name'

# ---- Fixed argument infos ----
# The item value is OrderedDict that maps argument name to ArgumentInfo dict.
# Items are ordered in parameter defining order.
# `fixed` means positional-or-keyword and keyword-only combined.
#
assert InspectInfo.K_FIXED_ARG_INFOS == 'fixed_arg_infos'

# ---- Variable-positional argument infos ----
# The item value is list of ArgumentInfo dicts.
#
assert InspectInfo.K_VARPOS_ARG_INFOS == 'varpos_arg_infos'

# ---- Variable-keyword argument infos ----
# The item value is dict that maps argument name to ArgumentInfo dict.
#
assert InspectInfo.K_VARKWD_ARG_INFOS == 'varkwd_arg_infos'

# ---- Missing argument infos ----
# The item value is OrderedDict that maps argument name to ArgumentInfo dict.
# Items are ordered in parameter defining order.
#
assert InspectInfo.K_MISSING_ARG_INFOS == 'missing_arg_infos'

# ---- Duplicate keyword argument infos ----
# The item value is dict that maps argument name to ArgumentInfo dict.
#
assert InspectInfo.K_DUPKWD_ARG_INFOS == 'dupkwd_arg_infos'
```

### Understand ArgumentInfo
Code:
```
from aoikinspectargs import ArgumentInfo
from aoikinspectargs import ArgumentType
from aoikinspectargs import ParameterType


# Create argument info
arg_info = ArgumentInfo(
    name='a',
    value=1,
    type=ArgumentType.POSITIONAL,
    param_type=ParameterType.POSITIONAL_OR_KEYWORD,
    pos_index=0,
    varpos_index=None,
)


# ----- ArgumentInfo -----
# ArgumentInfo instances are AttrDict instances.
# Below is the meaning of ArgumentInfo dict keys (accessible as attributes).

# ---- name ----
# Argument name.
#
assert arg_info.name == 'a'

# ---- value ----
# Argument value.
#
assert arg_info.value == 1

# ---- type ----
# Argument type.
#
assert arg_info.type == ArgumentType.POSITIONAL

# ---- param_type ----
# Parameter type.
#
assert arg_info.param_type == ParameterType.POSITIONAL_OR_KEYWORD

# ---- pos_index ----
# Positional argument index.
#
assert arg_info.pos_index == 0

# ---- varpos_index ----
# Variable-positional argument index.
#
assert arg_info.varpos_index is None
```

### Understand ArgumentType
Code:
```
from aoikinspectargs import ArgumentType


# ----- ArgumentType -----
# ArgumentType is enumeration class.
# Below is its enumeration values.

# ---- Positional argument ----
# The argument is given as positional value.
#
ArgumentType.POSITIONAL

# ---- Keyword argument ----
# The argument is given as keyword value.
#
ArgumentType.KEYWORD

# ---- Keyword-only argument ----
# The argument is given as keyword value and the parameter is keyword-only.
#
ArgumentType.KEYWORD_ONLY

# ---- Variable-positional argument ----
# The argument is given as variable-positional value.
#
ArgumentType.VAR_POSITIONAL

# ---- Variable-keyword argument ----
# The argument is given as variable-keyword value.
#
ArgumentType.VAR_KEYWORD

# ---- Default argument ----
# The argument is not given and the parameter has default value.
#
ArgumentType.DEFAULT

# ---- Missing argument ----
# The argument is not given and the parameter has no default value.
#
ArgumentType.MISSING
```

### Understand ParameterType
Code:
```
from aoikinspectargs import ParameterType


# ----- ParameterType -----
# ParameterType is enumeration class.
# Below is its enumeration values.

# ---- Positional-only parameter ----
# The parameter accepts positional argument.
#
ParameterType.POSITIONAL_ONLY

# ---- Positional-or-keyword parameter ----
# The parameter accepts positional or keyword argument.
#
ParameterType.POSITIONAL_OR_KEYWORD

# ---- Keyword-only parameter ----
# The parameter accepts keyword-only argument.
#
ParameterType.KEYWORD_ONLY

# ---- Variable-positional parameter ----
# E.g. `args` for `*args`.
# The parameter accepts variable-positional arguments.
#
ParameterType.VAR_POSITIONAL

# ---- Variable-keyword parameter ----
# E.g. `kwargs` for `**kwargs`.
# The parameter accepts variable-keyword arguments.
#
ParameterType.VAR_KEYWORD
```
