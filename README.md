# cdsSeqFileHandler
Class for reading/writing text files. Hides channels, handles BOMs, embedded CRLF and embedded quotes.

Class first shown as part of UK DataFlex Meetup 4th March 2021.

The idea of the class was to make reading/writing to a text file akin to reading/writing from/to a database table.

So, where we might go:

Get Main_File of oMyTable_dd to hFile
Send Request_Read of oMyTable_dd FIRST_RECORD hFile iIndex
While (found)
    Get_Field_Value hFile 1 to  ....
    Get_Field_Value hFile 2 to  ....
Loop

we could do:

Get Create (RefClass(cdsSeqFileHandler)) to hoFile
Set psFilename of hoFile to <filename>
Send Read_Line of hoFile FILE_FIRST_ROW  
While (found)
    Get Fetch_Row_Value of hoFile 1 to ....
    Get Fetch_Row_Value of hoFile 2 to ....
Loop
Send Destroy of hoFile

  

// Example Usage: 1. Reading (line by line) 
//
// Get CreateNamed (RefClass(cdsSeqFileHandler)) '_oFileHandler' to hoFileHandler
// Set psFileName of hoFileHandler to <FileName>
// Send Read_Line of hoFileHandler FILE_FIRST_ROW
// While (Found)
//     //.... and then either ....
//     Get Fetch_Row of hoFileHandler to sRow                    // returns whole row
//     //.... or .....
//     Get Fetch_Row_Value of hoFileHandler <iColumn> to sValue  // returns identified row value
//     //.... do whatever ....
//     Send Read_Line of hoFileHandler FILE_NEXT_ROW
// Loop
// Send Destroy of hoFileHandler
//
// *****
//
// Example Usage: 2. Reading (an entire file) 
//
// Get CreateNamed (RefClass(cdsSeqFileHandler)) '_oFileHandler' to hoFileHandler
// Set psFileName of hoFileHandler to <FileName>
// Get Read_File  of hoFileHandler to uaChar                            // a uChar array. BOM is removed automatically.
// Send Destroy of hoFileHandler
//
// *****
//
// Example Usage: 3. Reading (line by line, with Read and ReadLn) 
//
// Get CreateNamed (RefClass(cdsSeqFileHandler)) '_oFileHandler' to hoFileHandler
// Set psFileName   of hoFileHandler to <FileName>
// Get Open_Channel of hoFileHandler to iCh
// .... and then Read and ReadLn "as normal" (using iCh)
// ie. ReadLn Channel iCh to sData
//     Read   Channel iCh to sData
// .... BUT REMEMBER TO CHECK FOR A BOM (Byte Order Marker)
// .... which means that for the first VALUE/ROW read in you would need...
// Send RemoveByteOrderMark of hoFileHandler (&sData) 
//
// Send Close_Channel   // OPTIONAL because the Destroy will do this for you.
//
// Send Destroy of hoFileHandler
//
// *****
//
// Example Usage: 4. Writing (line by line)
//
// Get CreateNamed (RefClass(cdsSeqFileHandler)) '_oFileHandler' to hoFileHandler
// Set psFileName  of hoFileHandler to <FileName>
// Set psHeaderRow of hoFileHandler to 'Column One,Column Two,Column Three' // will be written as the first line. Optional.
// .... and then either ....
// Send Write_Line of hoFileHandler <sLine>                     // writes this complete line to a file (as a WriteLn)
//     .... or .....
// Send Clear    of hoFileHandler                               // Clears the row buffer. Important.
// Set Row_Value of hoFileHandler <iColumn Number> to sValue1
// Set Row_Value of hoFileHandler <iColumn Number> to sValue2
// Set Row_Value of hoFileHandler <iColumn Number> to sValue3
// Send Write_Line of hoFileHandler                             // with no argument it will look to write out the contents of the Row Buffer, set with "Set Row_Value"
//     .... whatever ....
// Send Destroy of hoFileHandler
//
// *****
//
// Example Usage: 5. Writing (line by line, with Write and WriteLn) 
//
// Get CreateNamed (RefClass(cdsSeqFileHandler)) '_oFileHandler' to hoFileHandler
// Set psFileName of hoFileHandler to <FileName>
// Get Open_Channel of hoFileHandler to iCh
// .... and then Write and WriteLn "as normal" (using iCh)
// ie. WriteLn Channel iCh sData
//     Write   Channel iCh sData
// .... BUT REMEMBER TO ADD A BOM (Byte Order Marker)
// .... which means that for the first VALUE/ROW written out you would need...
// Get AddByteOrderMark of hoFileHandler eBomUtf8 (&sData)
// Write/WriteLn Channel iCh sData
//
// Send Close_Channel   // OPTIONAL because the Destroy will do this for you.
//
// Send Destroy of hoFileHandler
//
// *****
//
// Example Usage: 6. Writing (an entire file) 
//
// Get CreateNamed (RefClass(cdsSeqFileHandler)) '_oFileHandler' to hoFileHandler
// Set psFileName  of hoFileHandler to <FileName>
// Send Write_File of hoFileHandler uaChar          // a uChar array. BOM is added automatically.
// Send Destroy of hoFileHandler
