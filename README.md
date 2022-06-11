# 4-bit-Johnson-counter-on-FPGA-by-using-the-concepts-of-verilog
module D_ff(
d_in , i_clk , rst_n , Q_out
);
input d_in , i_clk , rst_n ;
output reg Q_out ;
///reg Q_reg ;

//assign Q_out = Q_reg ;
always @(posedge i_clk or negedge rst_n)
begin
if(~rst_n)
begin
Q_out <= 1'b0;
end
else
begin
Q_out <= d_in ;
end
end


endmodule

module Top_module(i_clk , rst_n , o_Q);

input i_clk , rst_n ;
output wire [3:0] o_Q ;
wire clk_out ;
slow_clk CLK_GEN(.i_clk(i_clk) , .out_clk(clk_out));
D_ff U1(.d_in(~o_Q[0]) , .i_clk(clk_out) , .rst_n(rst_n) , .Q_out(o_Q[3]) );
D_ff U2(.d_in(o_Q[3]) , .i_clk(clk_out) , .rst_n(rst_n) , .Q_out(o_Q[2]) );
D_ff U3(.d_in(o_Q[2]) , .i_clk(clk_out) , .rst_n(rst_n) , .Q_out(o_Q[1]) );
D_ff U4(.d_in(o_Q[1]) , .i_clk(clk_out) , .rst_n(rst_n) , .Q_out(o_Q[0]) );

endmodule

`define COUNT_WIDTH 27
module slow_clk(i_clk , out_clk
);

input i_clk ;
output out_clk ;


parameter Count_limit = 50_000_000 ; ////fpga clock = 100MHZ , 100Mhz/2 = 50Mhz = 50, 000_000 = 2^24//

reg [`COUNT_WIDTH-1:0] cnt ;

reg temp_clk ;

always @(posedge i_clk)
begin
if(cnt==Count_limit-1)
begin
temp_clk <= ~temp_clk ;
cnt <= 0;
end
else
begin
cnt <= cnt+1'b1 ;
temp_clk <= 1'b0;
end

end

assign out_clk = temp_clk ;
endmodule

module test();

// Inputs
reg i_clk;
reg rst_n;

// Outputs
wire [3:0] o_Q;

// Instantiate the Unit Under Test (UUT)
Top_module uut (
.i_clk(i_clk),
.rst_n(rst_n),
.o_Q(o_Q)
);

initial begin
// Initialize Inputs
i_clk = 0;
rst_n = 0;

// Wait 100 ns for global reset to finish


// Add stimulus here

end


always #5 i_clk = ~i_clk ;

initial
begin
#4 rst_n = 1'b1 ;
#300 $stop ;
end
endmodule
