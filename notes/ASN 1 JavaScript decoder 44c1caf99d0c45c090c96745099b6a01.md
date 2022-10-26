# ASN.1 JavaScript decoder

Created: May 7, 2022 10:34 AM
Finished: No
Source: https://lapo.it/asn1js/
Tags: #tool

## Instructions

This page contains a JavaScript generic ASN.1 parser that can decode any valid ASN.1 DER or BER structure whether Base64-encoded (raw base64, PEM armoring and begin-base64 are recognized) or Hex-encoded.

This tool can be used online at the address [http://lapo.it/asn1js/](http://lapo.it/asn1js/) or offline, unpacking [the ZIP file](http://lapo.it/asn1js/asn1js.zip) in a directory and opening index.html in a browser

On the left of the page will be printed a tree representing the hierarchical structure, on the right side an hex dump will be shown. 
 Hovering on the tree highlights ancestry (the hovered node and all its ancestors get colored) and the position of the hovered node gets highlighted in the hex dump (with header and content in a different colors). 
 Clicking a node in the tree will hide its sub-nodes (collapsed nodes can be noticed because they will become *italic*).

OBJECT IDENTIFIER values are recognized using data taken from Peter Gutmann's [dumpasn1](http://www.cs.auckland.ac.nz/~pgut001/#standards) program.

### Links