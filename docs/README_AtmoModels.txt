Stellar atmosphere models are defined as functions in
popstar/atmospheres.py. These can be called by:

>from popstar import atmospheres
>atmo = atmospheres.<function_name>

To call an atmosphere for a particular star, user must define the
metallicity ([Z], so metallicity = 0 is solar), temperature (in K),
and gravity (in cgs):

>spectrum = atmo(metallicity=0, temperature=5800, gravity=4)
>wave = spectrum.wave # Wavelength in Angstroms
>flux = spectrum.flux # Flux in ergs s^-1 cm^-2 Angstrom^-1 (pysynphot
>Flam units)

Otherwise, one can pass the atmosphere function into an Isochrone
object, and that function will automatically be used to define the
spectrum of each star in the isochrone model.

PopStar uses the pysynphot framework to extract the model atmosphere,
and the the output spectrum is a pysynphot.Icat object (https://pysynphot.readthedocs.io/en/latest/ref_api.html#pysynphot.catalog.Icat).

The atmosphere model grids currently supported are:

-get_merged_atmosphere (default for stars)
-get_wd_atmospheres (default for white dwarfs)
-get_castelli_atmosphere
-get_phoenixv16_atmosphere
-get_BTSettl_2015_atmosphere
-get_BTSettl_atmosphere
-get_wdKoester_atmosphere
-get_kurucz_atmosphere
-get_phoenix_atmosphere
-get_nextgen_atmosphere
-get_amesdusty_atmosphere


========================
get_merged_atmosphere
========================
The get_merged_atmosphere function extracts a stellar atmosphere 
from a suite of different model grids, depending  on the input
temperature, (all values in K):

T > 20,000: ATLAS 
5500 <= T < 20,000: ATLAS
5000 <= T < 5500: ATLAS/PHOENIXv16 merge
3800 <= T < 5000: PHOENIXv16

For T < 3800, there is an additional gravity and metallicity
dependence.

If T < 3800 and [M/H] = 0: 
    T < 3800, logg < 2.5: PHOENIX v16
    3200 <= T < 3800, logg > 2.5: BTSettl_CIFITS2011_2015/ PHOENIXV16 merge
    3200 < T <= 1200, logg > 2.5: BTSettl_CIFITS2011_2015

Otherwise, if T < 3800 and [M/H] != 0:
    T < 3800: PHOENIX v16

References:
ATLAS: ATLAS9 models, Castelli & Kurucz 2004 (https://arxiv.org/abs/astro-ph/0405087)
PHOENIXv16: Husser et al. 2013 (https://ui.adsabs.harvard.edu//#abs/2013A&A...553A...6H/abstract)
BTSettl_CIFITS2011_2015: Baraffee+15, Allard+ 
(https://phoenix.ens-lyon.fr/Grids/BT-Settl/CIFIST2011_2015/SPECTRA/)

Additional Notes:
The ATLAS atmospheres are calculated with LTE, and so they
are less accurate when non-LTE conditions apply (e.g. T > 20,000 K). Ultimately we'd like to add a non-LTE atmosphere grid for
the hottest stars in the future.

Merged atmospheres are a weighted average between the two 
atmosphere grids in the given temperature range. Near one boundary one model
is weighted more heavily, while at the other boundary the other 
model is weighted more heavily. These are calculated in the 
temperature ranges where we switch between model grids, to 
ensure a smooth transition.

========================
get_wd_atmosphere
========================
This function is the default to extract atmospheres for white
dwarfs. It calls the atmosphere grid from Koester et al. 2010
(https://ui.adsabs.harvard.edu//#abs/2010MmSAI..81..921K/abstract). If
the desired WD parameters fall outside the grid, a blackbody spectrum
at the input temperature is returned.

========================
get_castelli_atmosphere
========================
The pysynphot ATLAS9 atlas (Castelli & Kurucz 2004,
http://www.stsci.edu/hst/observatory/crds/castelli_kurucz_atlas.html)

Teff: 3500 - 50000 K
gravity: 0 - 5.0 cgs
[M/H]: -2.5 - 0.2

========================
get_phoenixv16_atmosphere
========================
Reference: Husser et al. 2013
(https://ui.adsabs.harvard.edu//#abs/2013A&A...553A...6H/abstract)

-downloaded via ftp from website: http://phoenix.astro.physik.uni-goettingen.de/?page_id=15
-solar metallicity, solar [alpha/Fe]

Teff: 2300 - 7000 K, steps of 100 K; 7000 - 12000 in steps of 200 K
gravity: 0.0 - 6.0 cgs, steps of 0.5
[M/H]: -4.0 - 1.0

=========================
get_BTSettl_2015_atmosphere
=========================
Reference: Allard+12, Baraffe+15
CIFIST2011_2015 grid, downloaded from https://phoenix.ens-lyon.fr/Grids/BT-Settl/CIFIST2011_2015/FITS/ on 9/28/16

Teff: 1200 - 7000 K
gravity: 2.5 - 5.5 cgs
[M/H] = 0

=========================
get_BTSettl_atmosphere
=========================
Reference: Allard+12
CIFITS2011 grid, downloaded from https://phoenix.ens-lyon.fr/Grids/BT-Settl/

[M/H] = -2.5, -2.0, -1.5, -1.0, -0.5, 0, 0.5

Teff and gravity ranges depend on metallicity:

[M/H] = -2.5
Teff: 2600 - 4600 K
gravity: 4.5 - 5.5

[M/H] = -2.0
Teff: 2600 - 7000
gravity: 4.5 - 5.5

[M/H] = -1.5
Teff: 2600 - 7000
gravity: 4.5 - 5.5

[M/H] = -1.0
Teff: 2600 - 7000
gravity: Teff < 3200 --> 4.5 - 5.5; Teff > 3200 --> 2.5 - 5.5 

[M/H] = -0.5
Teff: 1000 -7000
gravity: Teff < 3000 --> 4.5 - 5.5; Teff > 3000 --> 3.0 - 6.0

[M/H] = 0
Teff: 750 - 7000
gravity: Teff < 2500 --> 3.5 - 5.5; Teff > 2500 --> 0 - 5.5

[M/H] = 0.5
Teff: 1000 - 5000
gravity: 3.5 - 5.0

Note on alpha enhancement:
For [M/H]= -0.0, +0.5 no anhancement
      [M/H]= -0.5 with [alpha/H]=+0.2
      [M/H]= -1.0, -1.5, -2.0, -2.5 with [alpha/H]=+0.4

=========================
get_wdKoester_atmosphere
=========================
White dwarf atmospheres
Reference: Koester et al. 2010
(https://ui.adsabs.harvard.edu//#abs/2010MmSAI..81..921K/abstract)


========================
get_kurucz_atmosphere
========================
The pysynphot Kurucz atlas (Kurucz 1993,
http://www.stsci.edu/hst/observatory/crds/k93models.html)

Teff: 3000 - 50000 K
gravity: 0 - 5 cgs
metallicity: -5.0 - 1.0

========================
get_phoenix_atmosphere
========================
The pysynphot PHOENIX atlas (Allard+03, 07, 09;
http://www.stsci.edu/hst/observatory/crds/SIfileInfo/pysynphottables/index_phoenix_models_html)
