# TNO MPC Lab - MPyC - Matrix Inverse

The TNO MPC lab consists of generic software components, procedures, and functionalities developed and maintained on a regular basis to facilitate and aid in the development of MPC solutions. The lab is a cross-project initiative allowing us to integrate and reuse previously developed MPC functionalities to boost the development of new protocols and solutions.

The package tno.mpc.mpyc.matrix_inverse is part of the TNO Python Toolbox.

This package is based on the demo `ridgeregression.py` from the MPyC library on Feb 26th, 2020, as implemented by
Frank Blom. https://github.com/lschoe/mpyc/blob/2de1dd76db632bdc2a48acfbbaab841fa73cf8bd/demos/ridgeregression.py. The
underlying theory is published in the paper 'Efficient Secure Ridge Regression from
Randomized Gaussian Elimination' by Frank Blom, Niek J. Bouman, Berry
Schoenmakers, and Niels de Vreede, presented at TPMPC 2019 by Frank Blom.
See https://eprint.iacr.org/2019/773 (or https://ia.cr/2019/773).

Note: we added support for secure fixed points (`SecFxp`).

*Limitations in (end-)use: the content of this software package may solely be used for applications that comply with international export control laws.*  
*This implementation of cryptographic software has not been audited. Use at your own risk.*

## Documentation

Documentation of the tno.mpc.mpyc.matrix_inverse package can be found [here](https://docs.mpc.tno.nl/mpyc/matrix_inverse/0.4.3).

## Install

Easily install the tno.mpc.mpyc.matrix_inverse package using pip:
```console
$ python -m pip install tno.mpc.mpyc.matrix_inverse
```

### Note:
A significant performance improvement can be achieved by installing the GMPY2 library.
```console
$ python -m pip install 'tno.mpc.mpyc.matrix_inverse[gmpy]'
```

If you wish to run the tests you can use:
```console
$ python -m pip install 'tno.mpc.mpyc.matrix_inverse[tests]'
```

## Usage

## Usage

> `example.py`
> ```python
> import numpy as np
> from mpyc.runtime import mpc
> from tno.mpc.mpyc.matrix_inverse import matrix_inverse
> 
> 
> async def main():
>     X = (np.random.randint(low=-1000, high=1000, size=(5, 5)) / 10).tolist()
>     Xinv = np.linalg.inv(X).tolist()
>     await mpc.start()
> 
>     secfxp = mpc.SecFxp()
>     X_mpc = [[secfxp(x) for x in row] for row in X]
>     X_mpc = [mpc.input(row, 0) for row in X_mpc]
> 
>     inverse = matrix_inverse(X_mpc)
>     Xinv_mpc = [await mpc.output(_) for _ in inverse]
>     Xinv_mpc = [[float(xx) for xx in x] for x in Xinv_mpc]
> 
>     checker = mpc.matrix_prod(X_mpc, inverse)
>     checker = [await mpc.output(_) for _ in checker]
> 
>     diff = np.array(Xinv) - np.array(Xinv_mpc)
>     rel_diff = np.divide(
>         diff, np.array(Xinv), out=np.zeros_like(diff), where=np.array(Xinv) != 0
>     )
> 
>     await mpc.shutdown()
> 
>     print(f"X = \n{np.array(X)}\n")
>     print(f"Xinv = \n{np.array(Xinv)}\n")
>     print(f"Xinv_mpc = \n{np.array(Xinv_mpc)}\n")
>     print(f"X * Xinv_mpc = \n{np.array(checker)}\n")
>     print(f"max absolute diff = {np.abs(diff).max()}")
>     print(f"max relative diff (nonzero entries) = {np.abs(rel_diff).max()}")
> 
> 
> if __name__ == "__main__":
>     mpc.run(main())
> ```
