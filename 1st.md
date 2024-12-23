# NS-2 Simulation Script

```tcl
# Create a new simulator instance
set ns [new Simulator]

# Open trace files for output
set ntrace [open "prac1.tr" w]
set namfile [open "prac1.nam" w]

# Define the finish procedure
proc finish {} {
    global ns ntrace namfile
    close $ntrace      ; # Close the trace file
    close $namfile     ; # Close the NAM file
    exec nam prac1.nam &   ; # Start NAM
    set num_drops [exec grep -c "^d" prac1.tr]  ; # Count packet drops
    puts "The number of packet drops is $num_drops"  ; # Output result
    exit 0  ; # Exit simulation
}

# Set tracing
$ns trace-all $ntrace
$ns namtrace-all $namfile

# Create nodes
set n0 [$ns node]
set n1 [$ns node]
set n2 [$ns node]

$n0 label "TCP Source"
$n2 label "Sink"

$ns color 1 pink

# Create duplex links
$ns duplex-link $n0 $n1 1Mb 10ms DropTail
$ns duplex-link $n1 $n2 1Mb 10ms DropTail

# Set link orientations
$ns duplex-link-op $n0 $n1 orient right
$ns duplex-link-op $n1 $n2 orient right

# Set queue limits
$ns queue-limit $n0 $n1 10
$ns queue-limit $n1 $n2 10

# Set up TCP agents
set tcp0 [new Agent/TCP]
$ns attach-agent $n0 $tcp0

set sink0 [new Agent/TCPSink]
$ns attach-agent $n2 $sink0

$ns connect $tcp0 $sink0

# Set up CBR application
set cbr0 [new Application/Traffic/CBR]
$cbr0 set packetSize_ 100
$cbr0 set rate_ 1Mb
$cbr0 attach-agent $tcp0
$cbr0 set random_ false

# Schedule events
$ns at 0.0 "$cbr0 start"
$ns at 5.0 "finish"  ; # Correctly call the finish procedure

# Run the simulation
$ns run
