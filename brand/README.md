# Brand files

Logos for every TDC Office module. The Dental Dashboard serves them to the other modules as `/shared/logo-horizontal.png` and `/shared/logo-stacked.png`. Modules don't keep their own copies.

| File | Use |
|---|---|
| `horizontal1.png` | Header bar on every page (served as `/shared/logo-horizontal.png`) |
| `stacked2.png` | Login page and dashboard home (served as `/shared/logo-stacked.png`) |

The originals are in Ryan's Google Drive folder **TDC LOGOS**, with PDF versions in its PDF subfolder. Agents can't reach that Drive, so the copies used by the site live here.

## Colours (sampled from the logo)

| Name | Hex | Use |
|---|---|---|
| Forest green | `#51573D` | Primary: buttons, active nav, headings |
| Slate navy | `#23333D` | Header bar, secondary accents |
| Black | `#000000` | Body text (or a near-black like `#1B1F23`) |
| White | `#FFFFFF` | Background |

The Dashboard turns these into the `--tdc-*` CSS variables in `/shared/tdc.css`. Status colours (ok / warn / alert) are chosen by the Dashboard for readability.
