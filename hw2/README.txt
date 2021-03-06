#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-
Ονοματεπώνυμο					ΑΕΜ		E-mail

Σωτήριος-Παναγιώτης Χύτας		2012	schytas@uth.gr
Κλάιντι Μποντούρρι 				2020	kbontourri@uth.gr
Ελευθέριος-Παναγιώτης Λούκας 	2029	eloukas@uth.gr

#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-#-

Όσον αφορά τα configs:
	Υπάρχουν 3 configs με τα εξής ονόματα :
	1)interactive
	2)non_interactive
	3)starvation_exp_burst
	
	Όλα τους είναι με μη αμελητέο χρόνο εκτέλεσης και χρησιμοποιήθηκαν για το όλο report,
	όπως θα διαπιστώσετε και παρακάτω.

Όσον αφορά το Goodness:
	Παρατηρούμε πάρα πολύ υψηλές τιμές στο goodness μιας διεργασίας, κάθε φορά
    που μπαίνει στο running queue.

	Αυτό γίνεται για τον εξής λόγο:
	Το πρώτο κλάσμα της goodness ισούται με ~1 
	Στο δεύτερο κλάσμα τώρα, ο αριθμητής ισούται με έναν πολύ μεγάλο αριθμό
	ενώ ο παρονομαστής είναι 1.Αυτό γίνεται διότι η διεργασία *μόλις* μπήκε στο Queue,
	συνεπώς WaitingInRQ(k) = 0.

    Αυτό μας δείχνει ότι μια διεργασία δεν πρόκειται να τρέξει πάνω απο μία φορά
    σε 2 συνεχόμενα κβάντα (εκτός απο την περίπτωση που είναι μόνη της στο rq).
    Συνέπεια αυτού είναι ο αλγόριθμος να συμπεριφέρεται σαν τον RR σε περιπτώσεις
    όπου υπάρχουν στο rq *μόνο* non-interactive διεργασίες και φαίνεται σωστό διότι
    συνήθως οι non-interactive διεργασίες "τρώνε" όλο το κβάντο και δίνοντας σε όλες
    το ίδιο ποσοστό του χρόνου να τρέξουν και έτσι στο τέλος της ημέρας έχουμε 
    καλό χρόνο ολοκλήρωσης. (config αρχειο: non_interactive)

    Στην περίπτωση μόνο interactive ή και μαζί με non interactive, ο αλγόριθμος SJF
    με βάση το goodness πάντα θα επιλέγει την διεργασία που ταυτόχρονα "υπόσχεται"
    ότι θα τρέξει λίγο σε σχέση με το min expected burst καθώς και το πόσο έχει 
    "αδικηθεί" σε σχέση με αυτόν που περιμένει περισσότερο στο rq (1oς και 2ος όρος
    αντίστοιχα στον τύπο). Επομένως καθέ φορά αποφασίζει δίκαια και έξυπνα ταυτόχρονα,
    για το ποια θα είναι η επόμενη διεργασία. Αυτό φαίνεται να γίνεται ξεκάθαρο
    όταν έχουμε *μόνο* interactive διεργασίες όπου γίνονται συνέχεια interrupts
    και έτσι βλέπουμε για κάθε διεργασία την "ανάγκη" της να τρέξει. 
    (config αρχειο: non interactive)
	
Όσον αφορά το expected burst:
   
    O SJF με βάση το expect burst είναι λίγο "χαζός" συγκριτικά με το goodness.
    Αυτό γίνεται γιατί δε λαμβάνει υπόψη τον χρόνο που μια διεργασία περιμένει στο rq,
    καθώς επίσης το  ότι μία διεργασία ξεκινάει με expected burst = 0, δηλαδή με το 
    που έρθει μια διεργασία, παίρνει την CPU.

	Γι αυτό όμως, υπάρχει περίπτωση λιμοκτονίας με τη χρήση μόνο του exp_burst.
	Για παράδειγμα, υπάρχει πιθανότητα να υπάρχουν διεργασίες που περιμένουν στην ουρά
	αλλά ποτέ δεν εκτελούνται διότι μπορεί να έρχονται άλλες νέες συνεχώς ή άλλες να 'παίρνουν' τη θέση της
	λόγω μικρότερης τιμής του expected burst. (config αρχείο: starvation_exp_burst)

    Επίσης, παρατηρήθηκε ότι η επιλογή της επόμενης διεργασίας εξαρτάται απο το
    spawn time της. Για παράδειγμα (όπως και στο simple.conf), έστω ότι η 1η διεργασία
    έρχεται την στιγμή 40 ms ενώ 2η την στιγμή 50 ms τότε θα δούμε ότι τρέχει η 1η 
    για 2 κβάντα και μετά η 2η για άλλα και πάει λέγοντας. Αν η 2η έρθει την στιγμή
    60 ms τότε θα γίνεται ανά 3 κβάντα κτλ κτλ. Αυτό συμβαίνει λόγο ότι το expected
    burst κάθε διεργασίας προσπαθεί να φτάσει ή να πέρασει το min expected burst ώστε
    να αλλάξει η διεργασία που έτρεχε. Έτσι όμως έχουμε ένα αρνητικό, π.χ έστω ότι η 1η
    διεργασία της απομένουν αλλά 5 ms ώστε να τερματίσει και γίνεται spawn μια 2η διεργασία,
    τότε η 1η που της είχα απομείνει πολύ λίγο θα αναγκαστεί να περιμένει την 2η να φτάσει 
    το δικό της expected burst. Όλο αυτό λόγου οτι με το expected burst δεν μας νοιάζει
    πόσο μια διεργασία περίμενει στο rq.

    Μια πολύ ακραία περίπτωση!!:
	Ακόμη, σε περίπτωση πολλών διεργασιών που πραγματοποιούν ~μηδενικό I/O (non-interactive διεργασίες)
	ο SJF μοιάζει σαν Round Robin(RR), αρκεί οι διεργασίες να κάνουν spawn την ίδια ακριβώς στιγμή.
	
Όσον αφορά goodness έναντι expected burst:

	Η χρήση του goodness είναι η λύση στο παραπάνω παράδειγμα λιμοκτονίας (του exp_burst) καθώς πλέον παίζει ρόλο
	και το πόσο πολύ περιμένει μια διεργασία στο RQ (waiting_in_rq). Αυτό λύνει και την 2η περίπτωση που αναφέρθηκε
    στο expected burst, και πάλι λόγο ότι το goodness βλέπει και το waiting_in_rq!

    Βέβαια, δεν υπάρχει κάποιος αλγόριθμος καλύτερος απο τον άλλον όσο αναφορά το scheduling.
    Τον κυριότερο ρόλο πάντα παίζει σε τι σύστημα θα χρησιμοποιηθεί ο αλγόριθμος scheduling!

Γενικότερα για τον αλγόριθμο χρονοδρομόλογησς SJF : 
	Εύκολος 'άπληστος' αλγόριθμος για να υλοποιηθεί. Μπορεί να είναι pre-emptive και non-preemptive.
	Βέβαια, υπάρχουν ελαττώματα όπως το ότι μπορεί να προκαλέσει λιμοκτονία ή αδικία(μονοπώλειο διεργασιών πχ)
	Κάτι άλλο πιο σημαντικό είναι ότι υπάρχει η απαίτηση να ξέρουμε πότε μια διεργασία θα τελειώσει, γεγονός πρακτικά αδύνατο στις περισσότερες περιπτώσεις.
	Όμως, υπάρχουν σχετικά αποτελεσματικοί τρόποι για να προσεγγίσουμε και να κάνουμε 'μαντεψιές' για αυτούς τους χρόνους(πχ μέσος όρος προηγούμενων χρόνων εκτελέσεων)
	
	Τα παραπάνω τον αξιολογούν ως έναν αρκετά καλό αλγόριθμο για *long-term ειδικά* CPU scheduling με μεγάλο throughput ο οποίος ελαχιστοποιεί
	για καθε διεργασία το μέσο όρο αναμονής της, έχοντας όντως αποτελέσματα.

***Σημείωση***
Μέγαλο ρόλο έχει το πόσο είναι το κβάντο!
Ειδικά στις περιπτώσεις που ο SJF συμπεριφέρεται σαν RR!
