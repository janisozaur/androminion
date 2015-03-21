# Launching Androminion with specific cards from another app #

You can launch the Androminion Intent with the cards to play with as shown in these two sample methods. The first will get the Intent if Androminion is installed, the second will launch that intent with the cards.

Note that the bane card can be specified by adding "bane+<card name>" to the name. The card names passed in will be stripped of any characters besides letters and numbers and compared to our version of the card name, which is (hopefully) the exact name of the physical card (stripped of non-letters and numbers as well).

Only the kingdom cards need to be passed it, but if cards like Copper are passed in, they will be ignored.

```

    private Intent getAndrominionIntentIfAvailable(Context context) {
        final PackageManager packageManager = context.getPackageManager();
        final Intent intent = new Intent(Intent.ACTION_MAIN);
        intent.setComponent(new ComponentName("com.mehtank.androminion","com.mehtank.androminion.Androminion"));
        list = packageManager.queryIntentActivities(intent, 0);
        if(list.size() > 0) {
            return intent;
        }
        
        return null;
    }

	private void launchAndrominion() {
	    try {
    	    if(cards != null && cards.size() > 0) {
    	        Intent intent = getAndrominionIntentIfAvailable(this);
        	    ArrayList<String> cardNames = new ArrayList<String>();
        	    for(Card card : cards) {
        	        StringBuilder sb = new StringBuilder(); 
                        if(getResult() != null && getResult().getBaneCard() != null && getResult().getBaneCard().equals(card)) {
                            sb.append("bane+");
                        }
        	        for(char c : card.getName().toCharArray()) {
        	            if(Character.isLetterOrDigit(c)) {
        	                sb.append(c);
        	            }
        	        }
        	        cardNames.add(sb.toString());
        	    }
        	    intent.putExtra("cards", cardNames.toArray(new String[cardNames.size()]));
        	    startActivity(intent);
    	        }
    	    }
    	    catch(Exception e) {
    	        e.printStackTrace();
    	    }
	}

```