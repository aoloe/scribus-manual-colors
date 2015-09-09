# Notes
- http://www.scribus-user.de/forum/viewtopic.php?f=7&t=104
- http://www.scribus-user.de/forum/viewtopic.php?f=7&t=101
- antonio_mo @ scribus-user.de/forum
  - Der Monitor ist nicht kallibriert : In dem Fall solltest Du sRGB wählen
  - Warum wählst Du genau dieses ICC-Profil? Hast Du es von Deinem Druckdienstleister aus den Druckbedingungen abgelesen?
- ein Textschwarz ist irgendwie anders definiert als normales 100%-Schwarz, vielleicht Rich Black oder so. Das nimmt man für große Schwarzflächen, die tiefschwarz erscheinen sollen, aber keinesfalls für Fließtext. (tipper @ scribus-user.de/forum)
- fast alle Druckereien wollen ein CMYK-PDF haben und geben ein PDF/X-3 vor was irrsinnig ist, weil diese PDF/X Spezifikation auch RGB in der PDF-Datei zulässt, daher sollte man ein PDF/X-1a immer vorziehen.  
  Wie aber hie rim aktuellen Fall zu sehen ist, sollte das nur jemand machen der weiss wie man saubere druckvorstufentaugliche PDFs erstellt, denn ein PDF/X ist kein Garant das es auch ein konformes PDF/X ist.  
  Ohne Preflight sollte eigentlich kein PDF in eine Druckerei geben. (antonio_mo @ scribus-user.de/forum)

## Color management

- the color management options in the PDF export dialog are only useful when target profiles are different from document profiles
- if you check the "embed profile for solid colors" options, the colors are inserted as is in the pdf and it's the viewer who will have to "apply" the profile. (no conversion of the RGB colors to CMYK)
- otherwise scribus makes sure that the color are defined in a way that matches the target (and converts them to cmyk if needed.  
  in this case there is no trace in the pdf of the ICC profiles used.
- with [extractICCprofiles.ps](http://code.openhub.net/file?fid=5ljC1ULaksYuKdlOp4Cjsq6EEVY&cid=AoGmYCR5TcA&s=&fp=293147&mp&projSelected=true#L0) from the ghostscript project you can check if / which color profiles are in your document.  
  for a definitive analysis, you should probably use a professional preflight tool... or at least adobe acrobat. 

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

- http://libregraphicsworld.org/blog/entry/getting-cmyk-colors-from-inkscape-to-scribus
- https://fedoraproject.org/wiki/How_to_set_CMYK_color_on_a_design_for_printing
