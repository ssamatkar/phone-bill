# phone-bill
public class PhoneBill {
	
	public static void main(String[] args) {
		String phoneLog = "00:05:23,9898989898"+ "\n" +
				  "01:00:23,1898989898" + "\n" +
				  "00:00:21,2898989898" + "\n" +
                  "00:00:23,2898989898" + "\n" +
                  "00:01:23,4898989898" + "\n" +
                  "00:05:55,5898989898" + "\n" +
                  "00:00:23,5898989898" + "\n" +
                  "00:02:23,7898989898" + "\n" +
                  "00:05:20,8898989898" + "\n" +
                  "00:00:23,1098989898" + "\n" +
                  "00:00:40,1898989898" + "\n" +
                  "00:05:23,2898989898";
		System.out.println(new PhoneBill().totalCost(phoneLog));
	}
	
	public int totalCost(String log) {
		
		//split the phoneLog into its individual rows
		String[] logEntries = log.split("\\n");
		
		// create a hashmap to store the phoneNumber and the total phone call duration for it
		HashMap<String, Integer> phoneNumberDetails = new HashMap<String, Integer>();
		
		for (String record: logEntries) {
			String[] details = record.split(",");
			int durationOfHours = Integer.valueOf(details[0].substring(0, 2)) * 3600;
			int durationOfMinutes = Integer.valueOf(details[0].substring(3, 5)) * 60;
			int durationOfSeconds = Integer.valueOf(details[0].substring(6, 8));
			int totalDuration = durationOfHours + durationOfMinutes + durationOfSeconds;
	
			// if we already have an entry for this phone number then add this total duration to the existing one else just add it
			if (phoneNumberDetails.containsKey(details[1])){
				totalDuration += phoneNumberDetails.get(details[1]);
				phoneNumberDetails.put(details[1], totalDuration);
			}
			else {
				phoneNumberDetails.put(details[1], totalDuration);
			}
		}
		
		/* Find the phone call with the maximum duration in event of a tie use the one with least numerical value  
		   (NOTE: I think this is redundant as we don't need to bother with the tie break given that they all have the same total duration and it's that value we will use
		    to calculate the total bill)
		*/
		int maxDuration = 0;
		int maxPhoneNumberValue = 0;
		String maxPhoneNumber = "";
		
		for (Map.Entry<String, Integer> entry : phoneNumberDetails.entrySet()) {
			int duration = entry.getValue();
			
			if (duration > maxDuration) {
				maxPhoneNumber = entry.getKey();
				maxPhoneNumberValue = Integer.valueOf(entry.getKey().replaceAll("-", ""));
				maxDuration = duration;
			}
			else {
				
				if (duration == maxDuration) {
					if (Integer.valueOf(entry.getKey().replaceAll("-", "")) < maxPhoneNumberValue) {
						maxPhoneNumber = entry.getKey();
						maxPhoneNumberValue = Integer.valueOf(entry.getKey().replaceAll("-", ""));
					}
				}
				
			}
		}
		
		/* Finally we can evaluate the total cost of the phone bill.
		   Rules:
		   (i) For a phone call less than 1 minutes no charge
		   (ii) For a phone call 1 minutes and longer charge 30 paise per started minute, so for a call of 1 minutes 2 seconds would be
		        charged as a 2 minute call i.e. 2*30 = 60 paise
		   (iii) All calls to the phone number with the longest total call duration are free. In the event that two phone numbers have the same total call duration use the one
		*/
		int totalCost = 0;
		
		for (Map.Entry<String, Integer> entry : phoneNumberDetails.entrySet())
		{
			if (entry.getKey().equals(maxPhoneNumber)) continue;
			
			if (entry.getValue() > 60) {
				totalCost += ((entry.getValue()/60) * 30);
				if (entry.getValue() % 60 != 0) totalCost += 30;
			}
			else {
				totalCost += (entry.getValue() * 3);
			}
		}
		
		return totalCost;
	}

}
