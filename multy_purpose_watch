module multy_purpose_watch(
    input clk, reset_p,
    input [4:0] btn,
    output [3:0] com,
    output [7:0] seg_7,
    output buzz_clk);
    
    parameter watch_mode        = 3'b001;
    parameter stop_watch_mode   = 3'b010;
    parameter cook_timer_mode   = 3'b100;
    
    wire [2:0] watch_btn, stopw_btn;
    wire [3:0] cook_btn;
    wire [15:0] watch_value, stop_watch_value, cook_timer_value, value;
    reg [2:0] mode;
    wire btn_mode;
    wire [3:0] btn_pedge;
    
    button_cntr btn_cntr0(.clk(clk), .reset_p(reset_p), .btn(btn[0]), .btn_pe(btn_pedge[0]));
    button_cntr btn_cntr1(.clk(clk), .reset_p(reset_p), .btn(btn[1]), .btn_pe(btn_pedge[1]));
    button_cntr btn_cntr2(.clk(clk), .reset_p(reset_p), .btn(btn[2]), .btn_pe(btn_pedge[2]));
    button_cntr btn_cntr3(.clk(clk), .reset_p(reset_p), .btn(btn[3]), .btn_pe(btn_pedge[3]));
    button_cntr btn_cntr4(.clk(clk), .reset_p(reset_p), .btn(btn[4]), .btn_pe(btn_mode));
    
    always @(posedge clk or posedge reset_p)begin
        if (reset_p) mode = watch_mode;
        else if (btn_mode) begin
            case(mode)
                watch_mode : mode = stop_watch_mode;
                stop_watch_mode : mode = cook_timer_mode;
                cook_timer_mode : mode = watch_mode;
                default : mode = watch_mode;
            endcase
        end
    end
    
    assign {cook_btn, stopw_btn, watch_btn} = (mode == watch_mode) ? {7'b0, btn_pedge[2:0]} :
                                              (mode == stop_watch_mode) ? {4'b0, btn_pedge[2:0], 3'b0} :
                                              {btn_pedge[3:0], 6'b0};
    
    loadable_watch(clk, reset_p, watch_btn, watch_value);
    stop_watch_csec stop_watch(clk, reset_p, stopw_btn, stop_watch_value);
    cook_timer cook(clk, reset_p, cook_btn, cook_timer_value, led, buzz_clk);

    assign value = (mode == cook_timer_mode) ? cook_timer_value :
                   (mode == stop_watch_mode) ? stop_watch_value :
                   watch_value;
                                             
    fnd_4digit_cntr fnd(.clk(clk), .reset_p(reset_p), .value(value), .seg_7_ca(seg_7), .com(com));
    
endmodule
