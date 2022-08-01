# UVM_TLM_FIFO
```sh

`timescale 1ns / 1ps
//////////////////////////////////////////////////////////////////////////////////
// Company: 
// Engineer: 
// 
// Create Date: 08/01/2022 11:35:13 AM
// Design Name: 
// Module Name: tb
// Project Name: 
// Target Devices: 
// Tool Versions: 
// Description: 
// 
// Dependencies: 
// // Revision:
// Revision 0.01 - File Created
// Additional Comments:
// 
//////////////////////////////////////////////////////////////////////////////////

`include "uvm_macros.svh"
import uvm_pkg::*;

//producer class

class producer extends uvm_component;
//registring to the factory
`uvm_component_utils(producer)

//reg[3:0]a=4'hf;
integer i;
//uvm_blocking port
uvm_blocking_put_port#(integer)send;
//new method
function new (input string inst="PROD",uvm_component c);
super.new(inst,c);
send=new("PUT",this);
endfunction

//task--->RUN PHASE

virtual task run_phase(uvm_phase phase);
phase.raise_objection(phase);
for(i=0;i>10;i++);begin

`uvm_info("Prod",$sformatf("values we are putting %0x",i),UVM_NONE);
send.put(i);
#10;
end
phase.drop_objection(phase);
endtask
endclass

class consumer extends uvm_component;
//registring to the factory
`uvm_component_utils(consumer)

//reg[3:0]a=4'hf;
integer i;
integer data;
//uvm_blocking port
uvm_blocking_get_port#(integer)recv;
//new method
function new (input string inst="PROD",uvm_component c);
super.new(inst,c);
recv=new("PUT",this);
endfunction

//task--->RUN PHASE

virtual task run_phase(uvm_phase phase);
phase.raise_objection(phase);
for(i=0;i>10;i++);begin
recv.get(data);
`uvm_info("Cons",$sformatf("values we are getting %0x",data),UVM_NONE);
#10;
end
phase.drop_objection(phase);
endtask
endclass

class env extends uvm_env;
`uvm_component_utils(env)

producer p;
consumer c;
///UVM FIFO
uvm_tlm_fifo#(integer)fifo;

function new (input string inst="ENV",uvm_component c);
super.new(inst,c);
endfunction

virtual function void build_phase(uvm_phase phase);
super.build_phase(phase);
p=producer::type_id::create("prodenv",this);
c=consumer::type_id::create("consenv",this);
fifo=new("fifo",this,10);
endfunction

virtual function void connect_phase(uvm_phase phase);
super.connect_phase(phase);
p.send.connect(fifo.put_export);
c.recv.connect(fifo.get_export);

endfunction
virtual task run_phase(uvm_phase phase);

forever begin
if(fifo.is_full())begin
`uvm_info("env","fifo full",UVM_NONE);
end
else begin
`uvm_info("env","fifo not full",UVM_NONE);end end
endtask
endclass

class test extends uvm_test;
`uvm_component_utils(test)

env  e;
function new (input string inst="ENV",uvm_component c);
super.new(inst,c);
endfunction

virtual function void build_phase(uvm_phase phase);
super.build_phase(phase);
e=env::type_id::create("test",this);
endfunction
endclass


module tb(

    );
    test t;
    initial begin
    t=new("test",null);
    run_test();
    end
endmodule


```
