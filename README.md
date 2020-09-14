![build](https://github.com/PhilippHochmann/ctable/workflows/build/badge.svg)
[![License: GPL v3](https://img.shields.io/badge/License-GPL%20v3-blue.svg)](http://www.gnu.org/licenses/gpl-3.0)

### To do
* Write documentation
* More assertions and checks (bad calls should not lead to segfaults)
* Remove MAX_COLS restriction (currently 11)

# ctable
Library to print nicely formatted tables to stdout.
Supports...
* cells spanning over multiple columns or rows
* ANSI color sequences
* alignment of numbers under decimal dot

Currently not supported...
* wchars
* special chars like ```\t```

## How to use it
Include ```src/table.h``` to use it. Invoke ```make``` to run tests.

First, get a new table with ```get_empty_table()```.
Its current column and current row are set to 0.
Cell insertions into the table always occur at the current column and current row.
After a cell insertion, the current column advances.
When styling a cell, call a setting-changing function *before* the cell insertion.

## Example
![Example Image](https://raw.githubusercontent.com/PhilippHochmann/ctable/master/example.png)
![Example Image](https://raw.githubusercontent.com/PhilippHochmann/ctable/master/example2.png)
The first example is generated by running the tests.

# Functions

## Data and printing
The following functions are used to obtain a new table, print it, and free it after use.

### Table get_empty_table()
Returns a new table without any set cells. Insertion begins at the upper left corner.
You don't need to look into a Table directly, it suffices to use the following functions to manipulate it.

### void print_table(Table \*table)
Prints a table to stdout. This function is equivalent to ```fprint_table(table, stdout)```.

### void fprint_table(Table \*table, FILE \*stream)
Prints a table to a specified stream.

### void free_table(Table \*table)
Frees all dynamic memory allocated for this table. It may not be used any more.

## Control
The following functions change the position of next cell insertion.

### void set_position(Table \*table, size_t x, size_t y)
Sets position of next cell insertion. ```x = 0``` is leftmost column, ```y = 0``` is first row.
Passing a large value for ```y``` may malloc a lot of rows, so use with care!

### void next_row(Table \*table)
Sets position of next cell insertion to the first column of next row relative to current insertion position.

## Cell insertion
These functions insert a cell at the current position and advances the position to the next column (in the same row).
When ```MAX_COLS``` many cells have been inserted into a row, ```next_row``` needs to be called.
An inserted cell can not be changed any more.

### void add_empty_cell(Table \*table)
Adds an empty cell without any content.

### void add_cell(Table \*table, char \*text)
Adds a cell with a specified text. You have to ensure that the passed pointer is still valid when the table is printed.
If you don't want to maintain the buffer yourself, use ```add_cell_fmt```.

### void add_cell_gc(Table \*table, char \*text)
The same as ```add_cell```, but frees the passed pointer on ```free_table```, so use with care!

### void add_cell_fmt(Table \*table, char \*fmt, ...)
Adds a cell with a text specified as if printed by ```printf```. Buffer allocation and cleanup will be taken care of.

### void add_cell_vfmt(Table \*table, char \*fmt, va_list args)
Flavor of ```add_cell_fmt``` that allows for va_lists, e.g. for a wrapper function.

### void add_cells_from_array(Table \*table, size_t width, size_t height, char \*\*array)
Adds multiple cells with contents specified by a memory-contiguous 2D-Array.
Insertion begins at current position, next position of insertion will be right to set cells in the same row.
Strings will not be copied, so take care that pointers within the array are valid when the table is printed!

## Settings
### void set_default_alignments(Table \*table, size_t num_alignments, TextAlignment \*alignments)
### void override_alignment(Table \*table, TextAlignment alignment)
### void override_alignment_of_row(Table \*table, TextAlignment alignment)
### void set_hline(Table \*table, BorderStyle style)
### void set_vline(Table \*table, size_t index, BorderStyle style)
### void make_boxed(Table \*table, BorderStyle style)
### void set_all_vlines(Table \*table, BorderStyle style)
### void override_left_border(Table \*table, BorderStyle style)
### void override_above_border(Table \*table, BorderStyle style)
### void set_span(Table \*table, size_t span_x, size_t span_y)
