# Front panel

12 HP Eurorack panel, 60.2 × 128.75 mm, made as an **aluminium PCB** by the PCB maker.

**How the print is made:** there's no silkscreen. The artwork is drawn in the solder-mask layer, **inverted**: the
graphics come from where the mask is and isn't, so the print is part of the board itself. That avoids the usual
trouble of silkscreen not gripping well on aluminium. The top copper adds plated rings around the jacks. Order it
with the mask colour of the variant you want and with no silkscreen.

| File | Layer |
|---|---|
| `algoradix-panel.GKO` | outline and every hole (jacks, pots, encoder, button, LED window, display window, the Core's connectors, rail slots) |
| `algoradix-panel.GTS` | top solder mask: **the artwork** |
| `algoradix-panel.GTL` | top copper: plated rings around the jacks |
| `algoradix-panel.DRL` | drill file (empty: all holes are milled from the outline) |

`variant-A` and `variant-B` share the outline and copper and differ only in the solder-mask artwork. They're the two
colourways in [the photos](../images/photos/04-panels-black-white.jpg).

<p>
<img src="../images/renders/panel-variant-A.svg" height="360" alt="variant A artwork">
<img src="../images/renders/panel-variant-B.svg" height="360" alt="variant B artwork">
</p>
