> **Decision Log — March 10, 2026:** The 0-10V output circuit will use a **DAC (MCP4728)** rather than a MOSFET-based PWM circuit. The DAC is a more modular and cleaner solution, producing true analog voltage output over I2C without the need for hardware clamping or low-end shutoff workarounds.

# General Plan
Create a carrier board that enables control of 0V - 10V control interface via Artnet over Ethernet. Primarily for controlling lights, but can be used for anything that has a 0-10V interface.
## Strategy
The board should be a fairly simple connector board that focusses on:
- Using Existing Components where possible. Primarily keeping it within the Adafruit Feather universe.
- Taking extra caution around the output voltage control to ensure clean 0 - 10V. Taking extra care to have actual 0V for shuttoff.

## Primary Components
### External to the board
#### POE Splitter
To keep the board simple/flexible we are not going to integrate the POE power conversion directly. This will simplify it and add flexibility if people want to power it separately from the ethernet connection.
They come in many styles, but this design should assume:
- 12V output
- Barrel jack connector
Example: https://www.amazon.ca/Splitter-Compliant-Surveillance-5-5x2-1mm-PS5712TG/dp/B08HS4NT13/ref=sr_1_1_sspa?crid=1QMILUBMJE5UV&dib=eyJ2IjoiMSJ9.qjiaAEkHX_ciUjygDwzVrvhpSM4aB2ip9joHXcnruMi0FMiHoXHM6p4XIkghST58dd-yLT--jLGvb5d62OazA2vGgmoiLev8ZmZwaY8nTqZPSeQynH1BFp7d0YrMgTAGPmGbkapsRqYaybVl3PyC8m_ldIp46_J-DaV4ygEO9GaVBU6CARkcv-Xzw0vA_MbvQaBhBB5OcRR8Wme9AmVGTjEuV5whuYaAcvBSsu_eBUl01LHicw362t2u2BxgumvpLRcP0p4cvfMsgY8Rff91htrGheyYNklsH4EerwxOK2I.o6O47zgXTvmvbdRnmpqBbV3-vlZXb1lmaHtPM1D5Fqg&dib_tag=se&keywords=poe%2Bsplitter%2B12v&qid=1773154827&sprefix=poe%2Bsplitter%2B%2Caps%2C134&sr=8-1-spons&sp_csd=d2lkZ2V0TmFtZT1zcF9hdGY&th=1

### Primary Board Components
#### Adafruit Feather TFT 5483
This is the one with the TFT on the front. I like it because the screen will be useful and we don't really need the button inputs used on the reverse. It also provides easier access to the STEMMA connector if we want to add other sensors later
- Digikey Link: https://www.digikey.com/en/products/detail/adafruit-industries-llc/5483/16592513?s=N4IgjCBcoLQBxVAYygMwIYBsDOBTANCAPZQDa4ArAEwIC6AvvYVWSBQCxwDMIDQA
- Adafruit Link: https://www.adafruit.com/product/5483

#### Adafruit Ethernet FeatherWing 3201
There are lots of ethernet breakouts, but they aren't much cheaper than the adafruit one. I've also used this one in production and had good results, and like keeping it within the Adafruit Ecosystem
- Digikey Link : https://www.digikey.com/en/products/detail/adafruit-industries-llc/3201/6165788
- Adafruit Link : https://www.adafruit.com/product/3201

### Secondary Board Components
#### Power Input
Simple Barrel Jack that will work with the POE Splitter

#### Power Output
Output voltage control will be handled by the **MCP4728 DAC** (see Secondary Board Components below), which produces clean analog voltage via I2C — avoiding the need for PWM clamping circuits and the 0V shutoff issues seen with MOSFET-based approaches.

#### MCP4728 DAC
4-channel, 12-bit DAC controlled over I2C. Provides clean 0–10V analog output (with appropriate output stage) and eliminates the hardware complexity of MOSFET-based PWM solutions. Limits the design to 4 output channels per chip, which aligns with the target output count.
#### Connectors
There are a lot of options here. At the moment I'm leaning toward the sort of pluggable screw connector. I think I want to keep them as 2 wire connectors.
- Plug Digikey Link: https://www.digikey.com/en/products/detail/molex/0395305002/2738464
- Socket Digikey Link: https://www.digikey.com/en/products/detail/molex/0395326002/2739051

# Open Questions
## Number of Outputs
In my mind this is somewhere between 4 - 8, depending on the form factor of the board, general complexity, etc. Note that using the MCP4728 DAC naturally yields 4 outputs per chip; adding a second chip would get to 8.

## ~~The 0-10V output circuit~~ (Resolved)
Decided: DAC (MCP4728) over MOSFET. See decision log at the top of this document.

---

# Component Summary Table

| Component | Status | Role | Notes |
|---|---|---|---|
| [POE Splitter (12V, barrel jack)](https://www.amazon.ca/Splitter-Compliant-Surveillance-5-5x2-1mm-PS5712TG/dp/B08HS4NT13/ref=sr_1_1_sspa?crid=1QMILUBMJE5UV&dib=eyJ2IjoiMSJ9.qjiaAEkHX_ciUjygDwzVrvhpSM4aB2ip9joHXcnruMi0FMiHoXHM6p4XIkghST58dd-yLT--jLGvb5d62OazA2vGgmoiLev8ZmZwaY8nTqZPSeQynH1BFp7d0YrMgTAGPmGbkapsRqYaybVl3PyC8m_ldIp46_J-DaV4ygEO9GaVBU6CARkcv-Xzw0vA_MbvQaBhBB5OcRR8Wme9AmVGTjEuV5whuYaAcvBSsu_eBUl01LHicw362t2u2BxgumvpLRcP0p4cvfMsgY8Rff91htrGheyYNklsH4EerwxOK2I.o6O47zgXTvmvbdRnmpqBbV3-vlZXb1lmaHtPM1D5Fqg&dib_tag=se&keywords=poe%2Bsplitter%2B12v&qid=1773154827&sprefix=poe%2Bsplitter%2B%2Caps%2C134&sr=8-1-spons&sp_csd=d2lkZ2V0TmFtZT1zcF9hdGY&th=1) | Confirmed | External power source | Not integrated on board; external, flexible |
| Adafruit Feather TFT 5483 ([Digikey](https://www.digikey.com/en/products/detail/adafruit-industries-llc/5483/16592513?s=N4IgjCBcoLQBxVAYygMwIYBsDOBTANCAPZQDa4ArAEwIC6AvvYVWSBQCxwDMIDQA) / [Adafruit](https://www.adafruit.com/product/5483)) | Confirmed | Microcontroller | Has TFT screen, STEMMA connector |
| Adafruit Ethernet FeatherWing 3201 ([Digikey](https://www.digikey.com/en/products/detail/adafruit-industries-llc/3201/6165788) / [Adafruit](https://www.adafruit.com/product/3201)) | Confirmed | Ethernet / ArtNet interface | Proven in production; stays within Adafruit ecosystem |
| Barrel Jack (power input) | Confirmed | Board power input | Mates with POE splitter output |
| Molex Pluggable Screw Connectors (2-wire) ([Plug](https://www.digikey.com/en/products/detail/molex/0395305002/2738464) / [Socket](https://www.digikey.com/en/products/detail/molex/0395326002/2739051)) | Confirmed | 0-10V output connectors | Plug: 0395305002 / Socket: 0395326002 |
| MCP4728 DAC (I2C, 4-ch) | Confirmed | 0-10V voltage control | Clean analog output; 4 channels per chip; I2C controlled |

