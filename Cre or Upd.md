# 24/10/2024
## We discussed on `created_on` and `Updated_on`
- we have to add Created_on and Updated_on in all `Student` , `Teacher` , `Department` and `School` model.
```
    created_on = models.DateTimeField(default=timezone.now, editable=False)
    updated_on = models.DateTimeField(auto_now=True)
```
for this we need to import 
```
from django.utils import timezone
from datetime import timezone
```