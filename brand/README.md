# Brand files

Logos, fonts and reference pictures for every TDC Office page. The Dental Dashboard copies these into `/shared/` and serves them to the modules. Modules don't keep their own copies. The full design rules are in [DESIGN.md](../DESIGN.md).

## Logos

| File | Use | Dashboard serves it as |
|---|---|---|
| `logo-horizontal.png` | Header on desktop and tablet (mark + TOLEDO DENTAL CARE), transparent background | `/shared/logo-horizontal.png` |
| `logo-mark.png` | Header on phones: circle, trees and tooth only, no words | `/shared/logo-mark.png` |
| `logo-horizontal-dark.png`, `logo-mark-dark.png` | The same for dark mode | `/shared/logo-horizontal-dark.png`, `/shared/logo-mark-dark.png` |
| `stacked2.png` | Login page and Dashboard home | `/shared/logo-stacked.png` |
| `horizontal1.png` | Original horizontal logo on white (source for the files above) | — |

Never stretch, squash or crop a logo: set the height, keep `width: auto`.

The originals are in Ryan's Google Drive folder **TDC LOGOS**, with PDF versions in its PDF subfolder. Agents can't reach that Drive, so the copies used by the site live here.

## Colours

The logo gives the base: slate navy `#23333D` and forest olive `#51573D`. All colours, including each module's accent and the status colours, are in [DESIGN.md §2](../DESIGN.md#2-colours). The Dashboard turns them into the `--tdc-*` CSS variables in `/shared/tdc.css`.

## Fonts

`fonts/`: Figtree, Archivo and JetBrains Mono as `.woff2` files, with their licences (SIL Open Font License). The Dashboard serves them from `/shared/fonts/`.

## Reference pictures

`reference/`: what the finished design looks like. Agents compare their pages with these.

| File | Shows |
|---|---|
| `0-shared-kit.png` | Colours, accents, status colours and fonts |
| `1-desktop-maintenance.png`, `2-desktop-lab.png`, `3-desktop-supply.png` | Desktop list pages: header, status tiles, banner, search, group by, rows |
| `4-phone-maintenance.png`, `5-phone-lab.png`, `6-phone-supply.png` | Phone layout: mark, slim banner, search, bottom tab bar with More |
| `7-desktop-dashboard.png` | Dental Dashboard home in the same family |

The pictures use sample numbers and made-up names. The editable originals are on Ryan's design canvas ("TDC apps – design options").
