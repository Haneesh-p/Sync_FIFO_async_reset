# Sync_FIFO_async_reset
---

## Synchronous FIFO with Asynchronous Reset

This module implements a synthesizable, parameterized **Synchronous FIFO (First-In, First-Out)** memory buffer designed in Verilog. While read and write data operations are synchronized to the rising edge of a single clock domain , the module features an asynchronous reset recovery mechanism.

### Key Features


**Single-Clock Data Domain:** Simplifies data transfers by performing operations on a single clock domain, eliminating clock domain crossing (CDC) complexity for data pathways.



**Active-Low Asynchronous Reset:** Internal read/write pointers and the data output register are cleared immediately when `rst` transitions low, independent of the clock.



**Pointer-Based Status Flags:** Utilizes an additional MSB bit in the pointer width (`add_width`) to reliably distinguish between completely `full` and completely `empty` states.



**Parametric Flexibility:** Supports custom tailoring of data architectures via adjustable parameters for data width and buffer depth.



---

### Port Descriptions

**clk** (Input, 1-bit): Master Clock signal. Synchronous operations occur on its rising edge.


 **rst** (Input, 1-bit): Active-Low Asynchronous Reset. Clears pointers and output immediately upon dropping low, without waiting for a clock edge.


 **wr_en** (Input, 1-bit): Write Enable. Drives data insertion into the FIFO memory array when high.


 **rd_en** (Input, 1-bit): Read Enable. Drives data extraction from the FIFO memory array when high.


 
**din** (Input, `[data_width-1:0]`): Parallel data input bus.


 
**dout** (Output, `[data_width-1:0]`): Registered parallel data output bus.


 **full** (Output, 1-bit): Status flag indicating the FIFO is full. Overflow protection logic ignores incoming writes when asserted.


 **empty** (Output, 1-bit): Status flag indicating the FIFO is empty. Underflow protection logic ignores incoming reads when asserted.



---

### Internal Architecture & Flag Logic

The FIFO utilizes a dual-port memory array (`mem`). The status flags are evaluated combinationally based on the mathematical relationship between the write pointer (`wr_ptr`) and read pointer (`rd_ptr`):


**Empty Condition:** Triggered when both pointers match exactly across all bits.



 
**Full Condition:** Triggered when the lower address bits match, but the Most Significant Bit (MSB) differs, indicating the write pointer wrapped around the memory boundary.




---

### Parameters


**data_width** (Default: `8`): The bit-width of each data word stored in the FIFO.


 
**depth** (Default: `16`): The maximum capacity of data words the memory array can hold.


 
**add_width** (Default: `4`): The internal address bus width required to completely index the memory space ($2^{\text{add\_width}} = \text{depth}$).



---

### Implementation Notes

> ⚠️ **Design Notice:** The internal tracking logic increments memory pointers using blocking assignments (`=`) directly inside the asynchronous sequential logic blocks. Ensure your downstream timing constraints and simulation environments match this structural setup to prevent pre/post-synthesis simulation mismatches.
> 
>
