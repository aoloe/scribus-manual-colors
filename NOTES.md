# Notes
- http://www.scribus-user.de/forum/viewtopic.php?f=7&t=104
- http://www.scribus-user.de/forum/viewtopic.php?f=7&t=101
- antonio\_mo @ scribus-user.de/forum
  - Der Monitor ist nicht kallibriert : In dem Fall solltest Du sRGB wählen
  - Warum wählst Du genau dieses ICC-Profil? Hast Du es von Deinem Druckdienstleister aus den Druckbedingungen abgelesen?
- ein Textschwarz ist irgendwie anders definiert als normales 100%-Schwarz, vielleicht Rich Black oder so. Das nimmt man für große Schwarzflächen, die tiefschwarz erscheinen sollen, aber keinesfalls für Fließtext. (tipper @ scribus-user.de/forum)
- fast alle Druckereien wollen ein CMYK-PDF haben und geben ein PDF/X-3 vor was irrsinnig ist, weil diese PDF/X Spezifikation auch RGB in der PDF-Datei zulässt, daher sollte man ein PDF/X-1a immer vorziehen.  
  Wie aber hie rim aktuellen Fall zu sehen ist, sollte das nur jemand machen der weiss wie man saubere druckvorstufentaugliche PDFs erstellt, denn ein PDF/X ist kein Garant das es auch ein konformes PDF/X ist.  
  Ohne Preflight sollte eigentlich kein PDF in eine Druckerei geben. (antonio\_mo @ scribus-user.de/forum)
- [My Open Source Photography Workflow](http://www.rileybrandt.com/2015/10/15/foss-photo-flow-2015/)


## Applying colors

### Applaying colors to shapes and frames

in the properties palette you have a color tab with two "sub-tabs": fill and outline

here you can set the color for the border and the fill of the frame.

if you want a borderless frame or shape:

- you can set the same color for the outline and the fill
- or set the color of the fill to "none" (in this case, you will have a "smaller" shape)

Scribus cannot create borderless shapes (and it seems that other DTP programs that pretend to so, simply hide the border from you without removing it).

### Applying colors to text



## Transparencies

## Gradients

not well supported in most PDF version

## Color management

see also <http://bugs.scribus.net/view.php?id=13346>

- the color management options in the PDF export dialog are only useful when target profiles are different from document profiles
- if you check the "embed profile for solid colors" options, the colors are inserted as is in the pdf and it's the viewer who will have to "apply" the profile. (no conversion of the RGB colors to CMYK)
- otherwise scribus makes sure that the color are defined in a way that matches the target (and converts them to cmyk if needed.  
  in this case there is no trace in the pdf of the ICC profiles used.
- with [extractICCprofiles.ps](http://code.openhub.net/file?fid=5ljC1ULaksYuKdlOp4Cjsq6EEVY&cid=AoGmYCR5TcA&s=&fp=293147&mp&projSelected=true#L0) from the ghostscript project you can check if / which color profiles are in your document.  
  for a definitive analysis, you should probably use a professional preflight tool... or at least adobe acrobat. 


If you have activated the Color Management and you are using "Printer" as a target in the PDF export , Scribus will convert all the colors to the target color profile. Since the colors are now been converted, the ICC profile is not embedded in the PDF file.

If you check the _Use Color Profile_ option for _Solid Colors_, the colors are not converted to the target profile and the profile itself is embedded into the PDF, so that it can be considered in the further processing steps.  
The same applies for the _Use color Profile_ option in the _Images_ section. 

If you your printer did not specify otherwise, the default behavior, with no ICC profile embedded, is what you are looking for.

It seems that for PDF/X export, _Use color Profile_ is checked and grayed out for images: that would mean that Scribus cannot convert the images to CMYK and expects the images to already be in CMYK or let them convert by the printer to CMYK according to the embedded profile.

## Using Scribus to produce CMYK output for Inkscape artwork

The workflow is as follows:

- get the ICC-profile your prospective printing centre recommends and link it in Inkscape ("Document Properties" → "Color Management")
- create your colors as "fill:#dc3125 icc-color(ISO-Coated-v2--ECI-, 0.01, 0.96, 0.90, 0.03)", i.e. RGB and profile-specific CMYK at the same time (but, is there an UI widget for it?)
- when assigning colors to objects first go to the CMS tab and choose the right profile
- save the file as plain SVG
- import the resulting SVG in Scribus
- choose the right ICC-profile in "Document Setup" → "Color Management"
- go to "Save as PDF" and before clicking "Save" choose "Color" → "... intended for: Printer"

resources:

- <http://libregraphicsworld.org/blog/entry/getting-cmyk-colors-from-inkscape-to-scribus>
- <https://fedoraproject.org/wiki/How_to_set_CMYK_color_on_a_design_for_printing>
- <http://bugs.scribus.net/view.php?id=3111>

## Grayscale

jghali: RGB images can be grayscale (3 channel/pixel) or real grascale (1 channel/pixel). for real grayscale, scribus in fact does nto use profiles and convert grayscale directly to black, which is usually the expected behavior in professional layout. separation with gimp will convert grayscale to cmyk using 4 inks, may provide better contrast, but won't be good for print stability

## rgb to cmyk

abusing scribus for converting images from rgb to cmyk:
- create a document with the rgb images
- activate the color management
- export to PDF 1.4 with printer as the target
- use `pdfimages --tiff` to export to tiff
- include the new images in your document.


## Checking if Scribus does the right thing

When you first start with Scribus, you might be tended to check if Scribus is doing the right thing. Often the person doing that are more geek than skilled in the DTP.

The result is that one spends lot of time checking the wrong things with the wrong tools.

TODO: move this chapter to a better place... ("Does Scribus do the right thing?")

### Embedding and outling fonts when exporting to PDF

There must be a paste somewhere else...

### Converting from RGB and CMYK

This section explains what is happening when you change back and forth between the RGB and CMYK representation of a color.

This is not explain how Scribus works for your real work, since you're not supposed to convert the colors in this way.

If the color matching matters to you, you should use the color code for the color space used in the document and let the color management do its work when exporting to PDF.

Anyway, here some explaination for you...


(Extract/transcript of a discussion between ScribusNoob and jghali on 23.02.2016, starting at 14:57 CET)

in the color editing dialog add a new color. make it RGB and set all values to 0. then change it to CMYK. you get CMY at 0% and K at 100%. everything's fine. now set it back to RGB and i  end up with something different than 0,0,0...  i get RGB values 22, 20, 19  
what's going on there?

if you keep switching between RGB and CMYK the values change again so it looks like some sort of rounding error

What is happening:
- RGB grays to CMYK conversion do not profiles for text or vector : those grays are converted to use K only because doing otherwise can trigger colorimetric stability issues at the printer do not profiles/do not use profiles
- CMYk to RGB conversions use profiles : in this case a color with K only is usually not the darkest color one can achieve, so conversion result is not RGB (0, 0, 0) and K is usually not perfectly neutral, so RGB values can be slightly different
- If color management is enabled conversion routines use profiles as specified in color management section of preferences and docuement setup. Otherwise, it uses the default profiles if they are installed

So the problem happens when converting from CMYK to RGB also for different values of K all RGB values are slightly different and much too bright  
RGB values end up being too bright, too? example: RGB set to 51, 51, 51 gives CMYK 0, 0, 0, 80%. converting back to RGB gives  84, 85,  86. Converting this back to CMYK gives 53.7, 44.7, 41.6, 47.84 % which in turn gives me RGB values 83, 84, 85
 icc profiles do not allow perfect round trips

What is happening:
- also all RGB colors cannot be perfectly converted to CMYK, so perect round trip is not even always possible the RGB dark point is much darker than what CMYK allows so there is already a whole domain in shadows which cannot be converted to CMYK without modifying color luminosity

I noticed that color swatches store their values as hex values between 0 and FF in their xml files. so even when i am not converting between RGB and CMYK i get rounding errors.  
example: new CMYK color. set K to 81%. OK. go back to edit the color. it now says 81.18%  
so the UI seems to suggest an accuracy that isn't actually there. the UI suggests i have 10000 steps for setting up CMYK values but the XML (and/or the actual code) seems to support only 256 steps.  
is there a reason for this? am i missing something?
colors are stored with 8bit precision currently, one day we'll switch to 16bit or floating point  
only reason is history

even with 16bit precision, that would not allow you to perform perfect RGB => CMYK => RGB round trips btw

seems irritating to the average user when she enters a value of 81% and the software changes it to 81.18%.  
wouldn't it be better in this case to also offer values between 0 and 255 for the CMYK controls? or would that go too much against the convention of how CMYK values are represented?

- The answer:
and we cannot prevent someone typing "81%" anyway, and users used to CMYK want to have values between 0 and 100 (lots of pro working in printing industry do not even understand the RGB scale between 0 and 255)

## Paper color

- In scribus you can define a paper color in the document settings. The color will be shown on the canvas but not exported to pdf.
- In the remote case, you might want to set a solid color that should be pritned, you will have to draw a colored rectange on a background layer.

on april 25, 2016, vlada writes to the mailing list:

Every color you want to print will add on top of another. If you print
yellow and lay down some blue on top of i, you'll end up with some shade of
green. You can do that as many times as you want and the result will be
always a step closer to a "rich" black. Normally, for acceptable
cost/efficiency ratio, we have CMYK (cyan+magenta+yellow+k) standard. Look
at Wikipedia for more information about it. You have hit the problem of
base color. It is simply assumed that underlying color is paper's white.
Everything is being build on top of it, quite literary.

Of course, you are not the first having an idea of printing on top of some
other color. Therefore, printers started using white pigment for making
special - white - color. Note the word Special here. We will return to that
soon. If you don't specify that you want old, timed, recycled, yellowish
paper, printers (both the people that operate printing presses and desktop
printers) will ASSUME you want it printed on plain white paper. If you
don't want that to happen, you should then go to a printing press operator
and handle your "special" paper to him. But, at that moment he makes one
more ASSUMPTION for you and that is that you want it to be printed using
CMYK method. Computer programs for prepress make those two assumptions for
you too. The background on your working canvas in Scribus is white -
non-printabe. And the colors you use are in CMYK.

What if you want to give printer your toothpaste, shoe polish or your
favourite perfume to print with that? It's possible, but you will not get
desired results with CMYK, of course. You have to define SPECIAL case. It's
the same with your white. In Scribus you should define specal color, called
Spot color, name it "White", and use it to fill objects with it. That's
already covered in detains in many other places. Part of it has been
documented in official documentation under Color Palettes.

What is also very important to you here is that it seems that white
background of an image is being treated in two ways:
1) Exactly as transparent, when put on canvas directly. There is no
distinction if your image has transparent background or not.
2) Removing other colors underneath it, when placed on top over some other
Scribus' item. Now there is a distinction. If your image contains
transparent pixels, underneath objects will appear where transparent. If it
were opaque, white background of your image will expose paper color at that
places.

In reality, white just exposes paper color.

## CMYK

if you don't know how to manage the colors, stick to RGB colors and talk to your print shop to figure out how he can help you to print the color the way you want them.

if your monitor is not profiled, all you can start with, is being very conservative with colors and ask for a proof and check if it's ok for you.

you have an RGB monitor and want the colors to be printed in CMYK on a printer.

you can do the conversion from the one space color to the other or you can let the print shop do it for you.

you can do the conversion from the one space color to the other or you can let the print shop do it for you.  
if you're a beginner, you should let the printer do the conversion or, at least, do it as late in the process as possible.

there is no black magic involved, but there are a few things to learn. more than i could ever teach you.

the short version is: if you do not have a specific CMYK color defined in your visual identitfy (or if your printer asks you to use CMYK colors), just forget about directly using CMYK colors and go for the RGB ones.  
if the printer does not accept the RGB PDF, simply get scribus to convert your colors to CMYK by using the fitting color profiles (ICC)

next step? activating the color management using it for creating the PDF..  
further step? asking the printer for the ICC profiles you should use.  
even further? get your monitor profiled and your room under controlled light.  
and once you will have got through all this, you will have learned enough about colors that you can start thinking about directly working with CMYK colors... you still feel it's a good idea (for the given project)
