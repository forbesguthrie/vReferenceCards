# Contributing
First off, thank you for considering adding to this community resource. All the small updates and additions make these cards what they are.  Contributing is super simple, and there's a variety of ways you can submit updates. Blog post on vReference.com to follow which I'll add here.

## Licensing
Be aware that you're contributing to a project licensed under the Creative Commons BY-NC-SA license, and by doing so you're accepting that fact. A link with more details is on the LICENSE.md file.

## Citations
If you can, please drop in the Git Commit message a specific link (for example a URL to a PDF with a page number, or article URL with quote) to where you found the discrepency.  If there's any dispute about the veracity of the change it's easy to check. If there isn't a citation, and we're not aware of the change, the merge request may not be accepted.

## Formatting
The HTML/PDF cards will be generated with the raw markdown (.md) files and some CSS to create something similar to the previous cards.  Markdown natively supports may of the formatting options we need, however where there are gaps we use basic HTML tags.  See the below sections for the breakdown.

Watch for double spaces at the end of line which forces a line break.

I use line breaks in the raw markdown file just to make it easier to read. This doesn't make any difference to the way it's rendered. 

### Card formatting with standard markdown
- ***Section header***:       			            prepend with ```##```
- ***Topic*** (bold):           	                enclose in two asterisks ```**foo**```
- ***Variable*** (italics):               		    enclose in one asterisk ```*bar*```
- ***CLI commands*** (red & monospaced):  		    enclose in single backticks ``` `code` ```
- ***URL linking*** (blue, underline):    		    URL with http(s)  
NB: shorten VMware's KB in this format: 	        ```https://kb.vmware.com/kb/123456```
- ***Comments*** not shown in HTML/PDF:			    ```<!--``` comment here ```-->```
- ***Line break*** (used before topic/sub-topic):   two space at end of line

### Card formatting that needs HTML tags (no support in markdown)
- ***Sub-topic*** (underline):            			```<u>  </u>```  
- ***Superscript***:                      			```<super>  </super>```  
- ***File/folder*** location (green):     			```<file>  </file>```  
- ***FIXME*** during card construction (highlight):	```<mark>  </mark>```  


### Unused formatting
- ***Section boxes*** (see below): 			        ```<div class="section">   </div>```

On my first kick at this project last year, I started adding DIV sections. When I was playing around with the markdown to PDF conversion, the DIV sectioning allowed me to create special formatting with some CSS tricks. I’ve left them in some sections on this reboot and will re-evaluate their usefulness shortly.