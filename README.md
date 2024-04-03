#!/usr/bin/perl
use strict;
use warnings;
my $froms="London";
my $tos="France";
my $tspare="\$5";
my $totseat=10;
my %s1=my %s2=();
sub ticket_available{
        (my $fname,my $lname,my $emil)=@_;
        my $cnta=my $cntb=0;
        if (-f "sessiona.txt" && -f "sessionb.txt"){
                open (A,"<sessiona.txt");
                open (B,"<sessionb.txt");
                while(<A>){
                        chomp $_;
                        my @sp = split(/\|/,$_);
                        $s1{$sp[2]}="$sp[0]|$sp[1]|$sp[2]";
                        $cnta++;
                }
                close(A);
                while(<B>){
                        chomp $_;
                        my @sp = split(/\|/,$_);
                        $s2{$sp[2]}="$sp[0]|$sp[1]|$sp[2]";
                        $cntb++;
                }
                close(B);
        }
        else{
                `echo "$fname|$lname|$emil">>sessiona.txt`;
                `echo "$fname|$lname|$emil">>sessionb.txt`;
        }
        my $avail = $totseat - ($cnta+$cntb);
        if ( $totseat >= $avail ){
                print "Total tickets available : $avail\n";
        }
        else{
                print "Sorry seats are filled , please try the next train. \n";
                exit;
        }
        print "Do you want to book the ticket ? type Y\n";
        chomp(my $btik=<STDIN>);
        uc($btik);
        if ($btik eq 'Y'){
                print "Ticket has been booked\n";
                print "Passanger details are below\n";
                print "Name : $fname$lname\n";
                print "Email : $emil\n";
                print "Seat No : session A-$avail\n";
        }


        return $cnta,$cntb;
}
sub cancel_ticket{
        my $cancnta=my $cancntb=0;
        (my $fname,my $lname,my $emil)=@_;
        if ( defined ($s1{$emil}) ){
                $cancnta++;
        }
        if ( defined ($s2{$emil}) ){
                $cancntb++;
        }
        if ( ($cancnta == 0) && ($cancntb == 0) ){
                print "Provided details are not available in passangers detail\n";
                print "Name:$fname $lname Email:$emil\n";
        }

        if ($cancnta > 0){
                `mv sessiona.txt tmp.txt`;
                open (A,">sessiona.txt");
                foreach my $i (keys%s1){
                        my @sp = split(/\|/,$s1{$i});
                        if ( $sp[2] != $emil ){
                                print A "$s1{$i}\n";
                        }
                }
                close(A);
        }
        if ($cancntb > 0){
                `mv sessionb.txt tmp.txt`;
                open (B,">sessionb.txt");
                foreach my $i (keys%s2){
                        my @sp = split(/\|/,$s2{$i});
                        if ( $sp[2] != $emil ){
                                print B "$s2{$i}\n";
                        }
                }
                close(B);
        }
}
print "Booking a train from London to France\n";
print "-------------------------------------\n";
print "Please enter the passanger first name\n";
chomp(my $pfname=<STDIN>);
if ( $pfname eq "" ){
        print "Passanger first name shouldnt be null\n";
        exit;
}
else{
        ucfirst($pfname);

}
print "Please enter the passanger last name\n";
chomp(my $plname=<STDIN>);
if ( $plname eq "" ){
        print "Passanger last name shouldnt be null\n";
        exit;
}
else{
        ucfirst($plname);

}

print "Please enter the passanger email\n";
chomp(my $pemail=<STDIN>);
if ( $pemail eq "" ){
        print "Passanger email shouldnt be null\n";
        exit;
}
(my $sa,my $sb)=&ticket_available($pfname,$plname,$pemail);
print "Do you want to cancel the ticket? if Yes type Yes else type No\n";
chomp(my $canc=<STDIN>);
$canc=uc($canc);
if ($canc eq 'YES' ) {
        &cancel_ticket($pfname,$plname,$pemail);
        print "Ticket has been cancelled for below passanger\n";
        print "Name : $pfname$plname\n";
        print "Email : $pemail\n";
        exit;
        print "Thank you wish you please visit again!!\n";
}
else {
        print "Thank you wish you happy journey !!\n";
        exit;
}
