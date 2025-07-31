# SayAloud
Windows app to Read Out Loud text. Meant to verify transcribed text against original on scans.

Suppose one has a task to transcribe text and store it in some organised form, e.g. a database.
Registers or indexes in archives are typical examples: all the time people are transcribing scans to make the contents accessible online.

This app allows to verify the input process, by reading out loud the text such that one sees the scan fullscreen and there is a small control panel in a corner.
It depends on the installation of the compiled Windows version of the eSpeak package (actually) on https://espeak.sourceforge.net/download.html. 


This is how the program is instructed:

Create a text file with rules, that direct the application. It contains:

- Optional on the first line: the options. If all defaults are ok, this line may be omitted. 

- One or more lines with the syntax rules for understanding the format of the text file to be read

- "One or more lines with the specification of how to speak" 


The simplest rule, which just speaks all the text of each line in english, could be: 
- -lang en
- all
- "all"

Options for the execution may be specified, with a minus as first character on the first line and options separated by a comma only.
- lang xx		where xx is the language to speak. 
- blank c		where c is the character written, where you mean to have a blank character.
- verbose		if included a verbose log will be written in ConsoleOutput.txt in the map that contains the program.
- default is: -lang nl,blank .
	

The rules file must be found in the map, that contains the program as: anyname.speak.txt
The program will ask which rules to use if more than 1 file is present in the directory, showing anyname as a hint.

Specification of syntax to divide the input text in parts to pronounce separatly.
Define each part as follows:
  - first specify the name of an identifier followed by 1 space, unless it is the last identifier.
  - then, have each (except the last one) identifier followed by a division string and again 1 space.
          After the last identifier omit the division string, indicating to take all until the end of the line.
          Division strings are formed by
          - a character that is not in the string itself, 
          - one or more characters that will separate this identifier from the next one,
          - again the character that started this.    
          for more than 1 division string put them in a list and separate them with a / (ie. '.'/',' identifier is separator from the next one by . or , )
          the literal 'TAB' as division will be treated as the single TAB symbol.  
          The separator can be any non-blank character not present in the part and not /.
Example.
if each line contains 3 words, with a space and a minus between them (as in: one two-three), 
the rule is w1 ' ' w2 '-' w3. This will split the line in 3 identifiers: w1 (= one), w2 (= two) and w3 (is three).
    
Specification the pronounciation:
Define the text to be spoken on as many lines as you like, between the 2 double quotes using any identifier or literal in any sequence, as follows:
   - a literal between single quotes, followed by a space. the literal cannot contain single quotes.
     the word PAUSE instead of a litteral will put the pronounciation in a halt, until you click to continue.
   - an identifier followed by a space. The identifier may be preceeded by :modifiers and followed by _modifiers to change the text before saying it. 
     :Modifiers are applied in order: T, 0, <, >, [], P, L, X, 9, E. 
     The identifier and modifiers cannot contain spaces. Substitute spaces with the space character from the -blank options. 
     - :0 -> remove leading zeroes from the identifier's text.
             e.g. :0number
     - :T -> Trim leading and trailing spaces and/or tabs from the text.
             e.g. :Ttext 
     - :P -> syntax :Poriginal_substitute:other . Remove the text of the identifier "original" (and the previous literal), 
             if the "original's" text is equal to the "other's" identifier text on this line.
             To substitute the "original's" text with a fixed one, put _text after the identifier.
             Finally put :otheridentifier after the identifier or its substituting text.
             :Pname_idem:othername. Speaks "idem" for name, if its contents is equal to othername's content. 
     - :L -> syntax :Loriginal_preceeding . Similar to :P, but here the text is compared with the text of the same identifier on the previous line. 
             e.g. :Lname removes preceding literal and contents of name, if it is equal to name on the previous line. 
             :Lname_idem speaks "idem" for name, if it is equal on the previous line. 
     - :9n -> split numbers up in groups of n digits.
              e.g. :92 splits 123456 in 12  34  56. :93 splits 123456 in 123  456. 
		if :0 is in effect, each part will be affected by the 0-removal.
     - :X[translation]..... -> translate one or more parts of the text of the identifier to a new text. Each translation consists of [old_new]
             If more than one "old -> new translation is specified, each translation will be applied to every occurrence of old in the text. 
             Translated text will thus be subjected to further translations that may follow.
             e.g. with option "blank ^", :X[£_^pound^][$_^dollar^] to name the currency, with a space before and after.
             or: :X[._][^_^^]number in the text of identifier number, first translates . in empty (removes the .); secondly translates one space in two spaces. 
                  Note that the speaker eSpeak considers 1.234 as two numbers, not one, and sees 1 234 as one number but 1  234 as two. 
     - :[code:translated,code:translated,.....] -> replace value of the text of the identifier (code) with a new text (translation). :X[] works on characters, :[] on identifiers.
       e.g. :[f:female,m:male]sex
     - :</head/ -> if text of the identifier starts with "head", delete that "head" part. 	/ can be any character but not present in "head"
     - :>/tail/ -> if text of the identifier ends with "tail", delete that "tail" part      	  or tail 
                   more heads or tails: are treated in the given order
       e.g. :>/./word removes a dot at the end of word.
     - :E -> remove the identifier's text and a preceeding literal, if the text to say is only blanks.
       e.g. 'if word not empty' :Eword


A real world example in the archives world, for verifying historic data entered in an application that registers birth certificates from the a Dutch Civil Registry.
After entering all data one can copy the grid that shows all entries of one year, resulting in a csv-type of spreadsheet.

For each registration record in the grid we have:
- an empty field followed by a TAB
- recording number, followed by a TAB.
- the date of recording, format dd-mm-yyyy followed by a TAB.
- name of the child with format lastname, forename(s)  followed by a TAB.
- the birth date of the child, format dd-mm-yyyy followed by a TAB.
- the sex of the child, m for male, v for female, blank for unknown/other followed by a TAB.
- name of the father with format lastname, forename(s) or N.N. (nomen nescio) followed by a TAB.
- name of the mother with format lastname, forename(s) followed by a TAB.
- indicator, whether a jpg of the registry scan is attached correctly.
 
This is an example of a part of the copied data. Columns separated by a TAB, names by a comma between last and first names:
  
    3 	08-03-1873 	Hospels, Maaijke	08-03-1873 	v 	Hospels, Otto 	    	Rooij, Elisabeth de 	  X  
    4 	27-03-1873 	Gorkum, Adriaan van	26-03-1873 	m 	N.N.          	    	Gorkum, Catharina van 	  X  
    5 	14-04-1873 	Starkenburg, Elisabeth	13-04-1873 	v 	Starkenburg, Pieter 	Groenveld, Jacomina 	  X  

I define the rules for the syntax of the line, to define the identifiers, followed by the pronouciation rules:

empty 'TAB' actNumber 'TAB' actDateDay '-' actDateMonth '-' actDateYear 'TAB' 
childLastname ',' childFirstname 'TAB' childBirthDay '-' childBirthMonth '-' childBirthYear 'TAB' childSex 'TAB'  
fatherLastname ','/'.' fatherFirstname 'TAB'  
motherLastname ',' motherFirstname 'TAB' scan

"
'akte ' actNumber ' van ' :0actDateDay ' ' :[01:januari,02:februari,03:maart,04:april,05:mei,06:juni,07:juli,08:augustus,09:september,10:oktober,11:november,12:december]actDateMonth 
'PAUSE' 'vader ' fatherFirstname ' ' fatherLastname 
'PAUSE' 'geboren op ' :0childBirthDay ' ' :[01:januari,02:februari,03:maart,04:april,05:mei,06:juni,07:juli,08:augustus,09:september,10:oktober,11:november,12:december]childBirthMonth 
'PAUSE' 'moeder ' motherFirstname ' ' motherLastname 
'PAUSE' 'kind ' childFirstname ' ' :PchildLastname:fatherLastname
'PAUSE' :T:[v:vrouw,m:man]childSex
"

It says (/ is a pause):
akte 3 van acht maart / vader Otto Hospels / geboren op acht maart / moeder Elisabeth de Rooij / kind Maaijke Hospels / vrouw 

