## Debugging

Jag undersökte felet där bokningar som överlappade registrerades som giltiga trots att den angivna tiden var upptagen. 
Ett exempel som jag började med var att skapa en bokning vars tid var 08:00-10:00 medan rummet var bokat 09:00-11:00. 
Bokningen som började 08:00 kunde fortfarande göras och räknades som giltig trots att den borde nekas. 
I och med att detta gällde ett fel som dyker upp vid skapandet av en bokning så letade jag upp metoden - i detta fall var det metoden "Create" som anropar "Validate" i "BookingService.cs". 
Jag hittade överlappningskontrollen som gjordes på bokningstiden och jämförde villkoren. Jag började med första delen av den:
```csharp
booking.StartTime >= existing.StartTime &&
booking.StartTime < existing.EndTime
```
Detta gjorde att 08:00 >= 09:00 gav ett "false" statement. Eftersom båda villkoren behövde vara sanna upptäcktes inte överlappningen. 
Den tidigare kontrollen använde inte den nya bokningens sluttid alls. 
Därför missade den att bokningen fortsatte i den upptagna tiden mellan 09:00-10:00. 
Jag ändrade villkoren så att kontrollen jämför den nya bokningens starttid med den befintliga bokningens sluttid och den nya bokningens sluttid med den befintliga bokningens starttid:
```csharp
booking.StartTime < existing.EndTime &&
booking.EndTime > existing.StartTime
```
Båda villkoren måste vara uppfyllda för att bokningarna ska räknas som överlappande. 
I koden används nu ">" och "<" utan "=" eftersom en bokning ska kunna börja när en annan slutar. 
Kontrollen gäller endast aktiva bokningar i samma rum och på samma datum. 
Bokningar i andra rum eller på andra datum ska inte blockera tiden. 
Avbokade bokningar ska inte heller hindra att tiden bokas igen.
För att kontrollera att ändringen fungerade så återskapade jag scenariot från innan där jag skapar en bokning med start 08:00 och slut 10:00 trots att det redan finns en bokning mellan 09:00-11:00. 
Den här gången nekades bokningen  och man fick ett felmeddelande. 
Jag kontrollerade också att en bokning som startade vid sluttid kunde registreras. 

Efter ändringarna och de manuella kontrollerna körde jag samtliga automatiserade tester. Alla 14 tester var gröna. 
De tester som kontrollerar detta beteende är "Create_WhenBookingOverlapsExistingBooking_ReturnsFailure" och "Create_WhenBookingStartsWhenExistingBookingEnds_ReturnsSuccess".
Tillsammans kontrollerar dessa tester både att upptagna tider nekas men även att en bokning får börja precis när en annan slutar. 

