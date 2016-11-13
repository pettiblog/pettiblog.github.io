---
title: "My Perl scripts"
excerpt: "Just some Perl I developed to analyse Biological Data."
header:
  teaser: perl.jpg
sidebar:
  - title: "My Perl scripts"
    image: perl.jpg
    text: "Just some Perl I developed to analyse Biological Data."
comments: true
---

Here are some of the Perl scripts I wrote for projects in my Master's course in order to analyse biological data. Perl is quite powerful, although I have opted to focus more on learning Python, so I highly doubt I will be writing any more Perl code anytime soon.

## ProNTo::IonStats

This Perl script is part of the ProNTo pipeline, a program designed to read a small prokaryotic genome, find the protein coding regions, translate the regions into proteins, digest the proteins and sort the ions according to their mass. IonStats is the last step of the pipeline, which takes the ion masses as input and sorts them into bins.

```perl
#!/usr/bin/perl

use diagnostics;
use strict;
use warnings;
use Getopt::Long;

# Defining bin size and parameters

print "Enter file name:\n";
my $data = <>;
#my $AAfilterContains = <>;
print "Enter m/z minimum range limit:\n";
my $massmin = <>;
print "Enter m/z maximum range limit:\n";
my $massmax = <>;
print "Enter binwidth:\n";
my $binwidth = <>;
my @sorted;
my $mzcolmatch;

handleopts();

sub handleopts {
        if (!GetOptions (
                "bin-width=i" => \$binwidth,
                "file=s" => \$data,
                "mass-min=i" => \$massmin,
                "mass-max=i" => \$massmax,
#                "aa-filter-contains=s" => \$AAfilterContains
                )) {
                print STDERR "Error, failed to obtain information from user\n";
                print "Help_page\n";
                exit(0);
        }
}
open(IFILE, $data) or die "Error, could not open input file\n";

my @lines = <IFILE>;
my @MZvalues;
my $AA;
my $line;
foreach my $line (@lines) {
#        if ($AAfilterContains =~ /[a-zA-z]/) {
#                my $MZvalue = processAA($line);
#        } else {
                my $MZvalue = processMZValue($line);
#        }
}
sub processMZValue {
        my $line = shift;
        next if ($line =~ /^$/);
        # this error-proofs the read so that at every loop it will ig$
        my @column = split( /\s+/, $line );
        # splits the column
        my $mzcol = $column[2];
        if ( $mzcol =~ /(\d+)/ ) {
                my $mzcolmatch = $1;
                # capture m/z value for the current row in table
                push @MZvalues, $mzcolmatch;
        }
}

print "$data was the file used.\n The binwidth is $binwidth.\n";
generateBins();
sub generateBins {
        my $i;
        my @finalCount;
        my $MZvalue;
        my $binline;
        my $binmin = $massmin;
        my $binmax = $massmin + $binwidth;
        my $num_bins = ($massmax - $massmin)/$binwidth;
        my $bin_id = 0; # start at bin 0
        # start loop with $binmin = $massmin and $binmax = $massmin + $binwidth
        for $MZvalue (@MZvalues) {
#               my @binned_mz_values;

#               SORT:
#                if ($MZvalue >= $binmin and $MZvalue <= $binmax) {

#                        addToCurrentBin($mzValue);
#                } else {
#                       while ($MZvalue > $binmax) {
#                               incrementBinBoundaries(); # increase $binmin and $binmax and return, exit if $binm$
#                               goto SORT;
#                       }
                if ($MZvalue >= $binmin and $MZvalue <= $massmax) {
                push @sorted, (int(($MZvalue-$massmin)/$binwidth));

                }

        }
        my @sortSorted = sort @sorted;
        for ($i = 0; $i < $num_bins; $i++) {
                $finalCount[$i]=0;
                foreach $binline (@sortSorted) {
                        if ($binline == $i) {
                                $finalCount[$i]++;
                        }
                }
                my $printA = $massmin + $binwidth * $i;
                my $printB = $massmin + $binwidth * ($i+1);
                print "BIN $printA\-$printB: $finalCount[$i]\n";
        }
}

exit;
```

## NIPAS

Granted it is a strange name for a script. This script I wrote during my first research project, where I was analysing the correlation between protein isoform multiplicity and protein abundance variability in the human proteome. This script is designed to input .tsb files containing the gene IDs linked to their transcript IDs, protein IDs and protein abundance. The order does not matter as the scripts checks it automatically. It then outputs a file containing all the genes along with the number of transcript and protein isoforms and the corresponding protein abundance.

```perl
#!/usr/bin/perl -w

use strict;

#########################################################################
#									#
#									#
#		 Number of Isoform and Abundance Sorter	 		#
#									#
#				NIPAS					#
#									#
#	     Outputs only genes with proteins and abundance		#
#									#
#########################################################################

my $infile = <>;
my $whereabund = <>;
my $abundincluded = 0;
if ($whereabund =~ /\d+/) {
	$abundincluded = 1;
} 
my $tcount = 0;
my $pcount = 0;
my $numabund = 0;
my $currentgene = "first";
my $firstrun = "first";
my $NA = "NA";

open(IFILE, $infile) or die "Cannot open the file $infile\n";

my $line;
my @findlocs;
my $protloc = 0;
my $geneloc = 0;
my $transloc = 0;
my $smallcount = 0;
my $errorcheck = 0;
my $x = 0;
my $y = 0;
my $firstline;
my $elementfind;
my $findloccolumnSize;
my @lines = <IFILE>;
my $findprot;

# first run to find what columns the data is in
foreach my $firstline (@lines){
	next if ($firstline =~ /ProtID/);
	# to find a representative row
	if ($firstline =~ /ENSP/ && $firstline =~ /ENSG/ && $firstline =~ /ENST/) {
		push @findlocs, $firstline;
		$smallcount++;
		# take the row and split it to find where the elements are
		for $elementfind (@findlocs) {
        		my @findcolumn = split(/\s+/, $elementfind);
                	$findloccolumnSize = @findcolumn;
			my $xmax = $findloccolumnSize-1;
			while ($x <= $xmax){
				my $lookforcolumn = $findcolumn[$x];
				if ($lookforcolumn =~ /ENSG/) {
					$geneloc = $y;
				}
                                if ($lookforcolumn =~ /ENST/) {
                                        $transloc = $y;
				}
                                if ($lookforcolumn =~ /ENSP/) {
                                        $protloc = $y;
				}
				$x++;
				$y++;
			}
			if ($abundincluded != 1) {
				$x = 1;
				$y = 1;
				while ($x <= $xmax){
                                	my $lookforcolumn = $findcolumn[$x];
                                       	if ($lookforcolumn =~ /\d+/ || $lookforcolumn =~ /NA/) {
						if ($x != $geneloc && $x != $transloc && $x != $protloc) {
							$whereabund = $y;
						}
                                        }
                               		$x++;
                                	$y++;
                        	}
			}
			if ($smallcount>0){
			goto RUN;
			}
		}
	}
}
RUN:

# print the header for the output table
print "GeneID\tTCount\tPCount\tAbundance\n";

# analyses the input file with for-loop
foreach $line (@lines) {
	
	# eliminates headers of input file
        next if ($line =~ /^ProtID/);
	next if ($line =~ /^\#/);
	next if ($line =~ /^Ensembl/);
	my @ncolumn = split(/\s+/, $line);
        my $genecolumn = $ncolumn[$geneloc];
	my $trans = $ncolumn[$transloc];
	my $prots = $ncolumn[$protloc];
	my $abundance = $ncolumn[$whereabund];
	# to check for errors in the data
	if ($genecolumn !~ /ENSG/ || $whereabund == 0) {
		if ($errorcheck == 0) {	
			print "Something went wrong.\nCheck your data, it may be corrupt!\nAlternatively check the code.\n";
			exit;
		}
	}
	# to print the last for-loop run in this version
	if ($genecolumn =~ /NA/) {
		if ($pcount>0) {
	       		print "$currentgene\t$tcount\t$pcount\t$numabund\n";
		}
		exit;
	}

	# to see if it is the first run 
	if ($currentgene ne $firstrun) {
		# to check for a change in gene name
		if ($currentgene ne $genecolumn) {
			if ($pcount>0 && $numabund != 0) {
				print "$currentgene\t$tcount\t$pcount\t$numabund\n";
			}	
			# reset counters
			$tcount = 0;
			$pcount = 0;
			$numabund = 0;
			if ($trans =~ /ENST/) {
                                $tcount++;
                      	}
                        if ($prots =~ /ENSP/) {
                               	$pcount++;
			}
                        if ($abundance =~ /\d+/) {
        	        	$numabund = $abundance;
	                }
		} else {
        		if ($trans =~ /ENST/) {
                		$tcount++;
		        }
			if ($prots =~ /ENSP/) {
        		        $pcount++;
			}
			if ($abundance =~ /\d+/) {
           	        	$numabund = $abundance;
	                }
		}
	} else {
		if ($trans =~ /ENST/) {
                	$tcount++;
                }    
               	if ($prots =~ /ENSP/) {
               		$pcount++;
                }
		if ($abundance =~ /\d+/) {
			$numabund = $abundance;
	   	}    
	}

	# to carry scanning the same gene or move on to next
	$currentgene = $genecolumn;
	$errorcheck++;
}

exit;
```

Feel free to comment and use the scripts. They could be highly improved, however I will not be working on them anymore as I have moved on to Python <i class="fa fa-sign-out"></i>.

