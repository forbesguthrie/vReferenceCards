## General
Watch for double spaces at the end of line which forces a line break.

I use line breaks in the raw markdown file just to make it easier to read. This doesn't make any difference to the way it's rendered. 

To format the text so it renders like the previous cards, use the following conventions.  I use standard markdown where possible, but some items need HTML tagging to get the achieved results.

## Card formatting with standard markdown
- Section header         					prepend with ## 
- Topic (bold)           					enclose in two asterisks **foo**
- Variable (italics)               			in *xxx*
- CLI commands (red & monospaced)  			enclose in backticks `code`
- URL linking (blue, underline)    			URL with http(s) 
**note**: shorten VMware's KB in this format: 	"https://kb.vmware.com/kb/123456"
- Comments (not shown in PDF)				<!-- comment here -->
- Line break (used before topic/sub-topic)	two space at end of line


## Card formatting that needs HTML tags (no support in markdown)
- Underline (sub-topic)            			<u>  </u>
- Superscript                      			<super>  </super>
- File/folder location (green)     			<file>  </file>
- FIXME things (highlight)					<mark>  </mark>

## Unused
- Section boxes (see below) 				<div class="section">  </div>

On my first kick at this project last year, I started adding DIV sections. When I was playing around with the markdown to PDF conversion, the DIV sectioning allowed me to create special formatting with some CSS tricks. I’ve left them in on this reboot and will re-evaluate their usefulness shortly.