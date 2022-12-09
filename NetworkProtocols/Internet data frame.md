[HDLC](https://en.wikipedia.org/wiki/High-Level_Data_Link_Control) (High-Level Data Link Control)
HDLC [frames](https://en.wikipedia.org/wiki/Data_frame "Data frame") can be transmitted over [synchronous](https://en.wikipedia.org/wiki/Synchronous_serial_communication "Synchronous serial communication") or [asynchronous serial communication](https://en.wikipedia.org/wiki/Asynchronous_serial_communication "Asynchronous serial communication") links. Those links have no mechanism to mark the beginning or end of a frame, so the beginning and end of each frame has to be identified. This is done by using a unique sequence of bits as a frame delimiter, or _flag_, and encoding the data to ensure that the flag sequence is never seen inside a frame. Each frame begins and ends with a frame delimiter. A frame delimiter at the end of a frame may also mark the start of the next frame.

On both synchronous and asynchronous links, the flag sequence is binary "01111110", or [hexadecimal](https://en.wikipedia.org/wiki/Hexadecimal "Hexadecimal") 0x7E, but the details are quite different.

### Synchronous framing[[edit](https://en.wikipedia.org/w/index.php?title=High-Level_Data_Link_Control&action=edit&section=3 "Edit section: Synchronous framing")]

Because a flag sequence consists of six consecutive 1-bits, other data is coded to ensure that it never contains more than five 1-bits in a row. This is done by [bit stuffing](https://en.wikipedia.org/wiki/Bit_stuffing "Bit stuffing"): any time that five consecutive 1-bits appear in the transmitted data, the data is paused and a 0-bit is transmitted.

The receiving device knows that this is being done, and after seeing five 1-bits in a row, a following 0-bit is stripped out of the received data. If instead the sixth bit is 1, this is either a flag (if the seventh bit is 0), or an error (if the seventh bit is 1). In the latter case, the frame receive procedure is aborted, to be restarted when a flag is next seen.

This bit-stuffing serves a second purpose, that of ensuring a sufficient number of signal transitions. On synchronous links, the data is [NRZI](https://en.wikipedia.org/wiki/NRZI#Non-return-to-zero_inverted "NRZI") encoded, so that a 0-bit is transmitted as a change in the signal on the line, and a 1-bit is sent as no change. Thus, each 0 bit provides an opportunity for a receiving [modem](https://en.wikipedia.org/wiki/Modem "Modem") to synchronize its clock via a [phase-locked loop](https://en.wikipedia.org/wiki/Phase-locked_loop "Phase-locked loop"). If there are too many 1-bits in a row, the receiver can lose count. Bit-stuffing provides a minimum of one transition per six bit times during transmission of data, and one transition per seven bit times during transmission of a flag.

When no frames are being transmitted on a simplex or full-duplex synchronous link, a frame delimiter is continuously transmitted on the link. This generates one of two continuous waveforms, depending on the initial state:

[![NrziEncodedFlags.png](https://upload.wikimedia.org/wikipedia/commons/c/c3/NrziEncodedFlags.png)](https://en.wikipedia.org/wiki/File:NrziEncodedFlags.png)

The HDLC specification allows the 0-bit at the end of a frame delimiter to be shared with the start of the next frame delimiter, i.e. "011111101111110". Some hardware does not support this.

For half-duplex or multi-drop communication, where several transmitters share a line, a receiver on the line will see continuous idling 1-bits in the inter-frame period when no transmitter is active.

HDLC transmits bytes of data with the least significant bit first (not to be confused with [little-endian](https://en.wikipedia.org/wiki/Little-endian "Little-endian") order, which refers to byte ordering within a multi-byte field).

### Asynchronous framing[[edit](https://en.wikipedia.org/w/index.php?title=High-Level_Data_Link_Control&action=edit&section=4 "Edit section: Asynchronous framing")]

When using asynchronous serial communication such as standard [RS-232](https://en.wikipedia.org/wiki/RS-232 "RS-232") [serial ports](https://en.wikipedia.org/wiki/Serial_port "Serial port"), synchronous-style bit stuffing is inappropriate for several reasons:

-   Bit stuffing is not needed to ensure an adequate number of transitions, as start and stop bits provide that,
-   Because the data is [NRZ](https://en.wikipedia.org/wiki/Non-return-to-zero "Non-return-to-zero") encoded for transmission, rather than NRZI encoded, the encoded waveform is different,
-   RS-232 sends bits in groups of 8, making adding single bits very awkward, and
-   For the same reason, it is only necessary to specially code flag _bytes_; it is not necessary to worry about the bit pattern straddling multiple bytes.

Instead asynchronous framing uses "control-octet transparency", also called "[byte stuffing](https://en.wikipedia.org/wiki/Byte_stuffing "Byte stuffing")" or "octet stuffing". The frame boundary octet is 01111110, (0x7E in [hexadecimal](https://en.wikipedia.org/wiki/Hexadecimal "Hexadecimal") notation). A "control [escape octet](https://en.wikipedia.org/wiki/Escape_character "Escape character")", has the value 0x7D (bit sequence '10111110', as RS-232 transmits least-significant bit first). If either of these two octets appears in the transmitted data, an escape octet is sent, followed by the original data octet with bit 5 inverted. For example, the byte 0x7E would be transmitted as 0x7D 0x5E ("10111110 01111010"). Other reserved octet values (such as [XON or XOFF](https://en.wikipedia.org/wiki/Xon/Xoff "Xon/Xoff")) can be escaped in the same way if necessary.

The "abort sequence" 0x7D 0x7E ends a packet with an incomplete byte-stuff sequence, forcing the receiver to detect an error. This can be used to abort packet transmission with no chance the partial packet will be interpreted as valid by the receiver.

[Inter frame gap](https://en.wikipedia.org/wiki/Interpacket_gap) (IFG)

typical size 64-150 bytes

[Useful video](https://www.youtube.com/watch?v=xrVN9jKjIKQ&list=PLowKtXNTBypH19whXTVoG3oKSuOcw_XeW&index=20&ab_channel=BenEater)