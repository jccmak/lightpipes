.. Index::
    User defined functions
    Cylindrical Lens
    userfunc.py
    __init__.py
    site-packages

User defined functions.
***********************

It is possible to make your own commands and use them in your simulations in the same way as the LightPipes commands. In this chapter we describe a step by step procedure to write a user defined command.

Find userfunc.py
----------------

The code of a user defined command must be put in the file: userfunc.py. This file can be found in the directory in which the LightPipes package was installed on your computer. This directory, called "site-packages" can be found at the place where Python was installed. Use the following Python commands to locate this directory. Depending on your Python-installation the response could be:

**Windows:**

.. code-block:: bash

    >>> import site
    >>> site.getsitepackages()
    ['C:\\Users\\Fred\\AppData\\Local\\Programs\\Python\\Python37', 'C:\\Users\\Fred\\AppData\\Local\\Programs\\Python\\Python37\\lib\\site-packages']
    >>> print(site.getsitepackages()[1])
    C:\Users\Fred\AppData\Local\Programs\Python\Python37\lib\site-packages
    >>>

**Mac:**

.. code-block:: bash

    >>> import site
    >>> site.getsitepackages()
    ['/Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/site-packages']
    >>> 


**Linux (ubuntu):**

.. code-block:: bash

    >>> import site
    >>> site.getsitepackages()
    ['/usr/local/lib/python3.6/dist-packages', '/usr/lib/python3/dist-packages', '/usr/lib/python3.6/dist-packages']
    >>> print(site.getsitepackages()[1])
    /usr/lib/python3/dist-packages
    >>> 

Write the code
--------------

As an example the code for a cylindrical lens is used here:

.. code-block:: bash

    def CylindricalLens(Fin,f,x_shift=0.0,y_shift=0.0,angle=0.0):
        """
        *Inserts a cylindrical lens in the field*
        
        :param Fin: input field
        :type Fin: Field  
        :param f: focal length
        :type f: int, float
        :param x_shift: shift in the x-direction (default = 0.0)
        :type x_shift: int, float
        :param y_shift: shift in the y-direction (default = 0.0)
        :type y_shift: int, float
        :param angle: rotation angle (default = 0.0, horizontal)
        :type angle: int, float    
        :return: output field (N x N square array of complex numbers).
        :rtype: `LightPipes.field.Field`
        :Example:
    
        >>> F=Begin(size,wavelength,N)
        >>> F=CylindricalLens(F,f) #Cylindrical lens in the center
        >>> F=CylindricalLens(F,f, x_shift=2*mm) #idem, shifted 2 mm in x direction
        >>> F=CylindricalLens(F,f, x_shift=2*mm, angle=30.0*deg) #idem, rotated 30 degrees
        
        .. seealso::
            
            * :ref:`Examples: Transformation of high order Gauss modes <Transformation of high order Gauss modes.>`
        """
        Fout = Field.copy(Fin)
        k = 2*_np.pi/Fout.lam
        yy, xx = Fout.mgrid_cartesian
        xx -= x_shift
        yy -= y_shift
        if angle!=0.0:
            cc = _np.cos(angle)
            ss = _np.sin(angle)
            xxr = cc * xx + ss * yy
            yyr = -ss * xx + cc * yy
            yy, xx = yyr, xxr
        fi = -k*(xx**2)/(2*f)
        Fout.field *= _np.exp(1j * fi)
        return Fout

Before the code begins it is recommended to insert a socalled "docstring" just after the function definition. In this docstring the working of the command and the parameters are explained and become visible in the help and eventually in the html pages of a sphinx web.
In the code the first thing to do is to copy the input field with the Field.copy(Fin) command. At the end the output field can be returned after application of the modifications by the mathematics and the input parameters.
It is recommended to put the parameters in the order as shown: the input field, Fin, first, next the required positional parameters and finally the optional parameters with their default values.

Modify the __init__.py file.
----------------------------

The final step is the modification of the __init__.py script. This file is also in the *.../site-packages/LightPipes* directory.
Find the Python list:

.. code-block:: bash

    ...
    #User defined functions from userfunc.py:
    __all__.extend([
        'FieldArray2D',
        'RowOfFields',
        'CylindricalLens', # user defined function added at June 9, 2020 by Fred.
        ])
    ...

and add your command to the list.

Finally you have to add your command to the line:

.. code-block:: bash

    ...
    from .userfunc import FieldArray2D, RowOfFields, CylindricalLens
    ...
    
Test your command.
------------------

Write a test program to test your command. For the CylindricalLens command the following test script was used:

.. plot:: ./Examples/Commands/UserDefinedCylindricalLens.py
    :include-source:

Your command should be an extension of LightPipes.
--------------------------------------------------

If you are convinced that your new command is an important, useful extension of the LightPipes for Python package, please open an issue on our `GitHub <https://github.com/opticspy/lightpipes>`_ site with the source code and the authors will consider to put it in a new version of LightPipes for Python.
