# perl-module_instance
# This script is used for module instance in integration.

#!/usr/bin/perl
$in=$ARGV[0];
if(!defined $in) {
    die "Usage:$0 filename";
}

my $out=$in;
$out=~s/(\.\w+)?$/.v/;

if(!open $in_fh, '<',$in) {
    die "cannot open '$in':$!";
}

if(!open $out_fh, '>',$out) {
    die "cannot open '$out':$!";
}

while(<$in_fh>)  {
    chomp;
    my @line = split(',',$_);
    if($line[0] eq "module") {
        $module_name = $line[1];
        print $out_fh $module_name."\n";
    }
    elsif ($line[0] eq "parameter") {
        print $out_fh "#(\n";
        my @pam = split(';',$line[1]);
        chomp;
        my $pam_len = @pam;
        if($pam_len > '1') {
            $pam_last = splice(@pam,-1);
            foreach $ele (@pam) {
                my @pam_ele = split('=',$ele);
                print $out_fh ".$pam_ele[0]($pam_ele[1]),\n";
            }
            my @last_ele = split('=',$pam_last);
            print $out_fh ".$last_ele[0]($last_ele[1])\n";
            print $out_fh ")\n";
        }
        else {
            my @pam_ele = split('=',$pam[0]);
            print $out_fh ".$pam_ele[0]($pam_ele[1])";
            print $out_fh ")\n";
        }
    }
    elsif($line[0] eq "Name") {
        print $out_fh "u_$module_name\n";
        print $out_fh "(\n";
    }
    elsif(lc($line[0]) eq "end") {
        print $out_fh ");\n";
    }

    if(lc($line[3]) ne "last") {
        if((uc($line[1]) eq 'I') or (uc($line[1]) eq 'O') or (uc($line[1]) eq 'IO')) {
            print $out_fh ".$line[0]     (       ),\n";
        }
    }
    elsif(lc($line[3]) eq "last") {
        if((uc($line[1]) eq 'I') or (uc($line[1]) eq 'O') or (uc($line[1]) eq 'IO')) {
            print $out_fh ".$line[0]     (       )\n";
        }
    }
}            

###############################################################################

module	lte_bp_secure		
parameter	AXI_AW=32;AXI_DW=32		
Name	I/O	Width	
clk_sys	I	1	
clk_core	I	1	
rstn_in	i	1	
rstn_in_sys	I	1	
awid_s	I	1	
awaddr_s	I	AXI_AW	
awlen_s	I	1	
awsize_s	I	1	
awburst_s	Io	4	
awlock_s	I	1	
awcache_s	IO	5	
awprot_s	I	1	
awvalid_s	I	1	
awready_s	o	1	
wid_s	I	1	
wdata_s	i	1	
b	I	3	last
end			





