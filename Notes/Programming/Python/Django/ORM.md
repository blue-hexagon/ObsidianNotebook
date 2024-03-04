# Querying FK & M2M's
## Data for examples
```python
class Group(models.Model):
    name = models.CharField(max_length=32, blank=False, null=False)
    date_created = models.DateTimeField(auto_now_add=True, blank=True)
    cover_image = models.ImageField()
    associated_country = models.CharField(max_length=50, blank=False, null=False) # TODO: Choices
    gps_coordinates = models.CharField(max_length=40, blank=False, null=False)


class GroupImage(models.Model):
    user = models.OneToOneField(User, on_delete=models.CASCADE)
    date_created = models.DateTimeField(auto_now_add=True, blank=True)
    image = models.ImageField()
    associated_group = models.ForeignKey("Group", on_delete=models.CASCADE)
```
# Querying One-To-Many (FK)
## Filtering Children based off of Parent Fields
```python
Child.object.filter(Parent__column="Value")
-> Queryset [<Child>,<Child>,<Child>]

GroupImage.object.filter(Group__associated_country="Denmark")
-> <QuerySet [<GroupImage: Id33>,<GroupImage: Id1>,<GroupImage: Id94>]

GroupImage.object.filter(Group__associated_country__startswith="De")
-> GroupImages from Denmark, Deutchland etc.
```
## Filtering parents based off of Child Fields
```python
Parent.object.filter(Child__column="Value")
-> Queryset [<Parent>,<Parent>,<Parent>]

Group.object.filter(GroupImage__user="@CosplayJulie")
-> <QuerySet [<Group: "Danmarks Kongehus">,<Group: "Den skæve ko">]

Group.object.filter(GroupImage__user__startswith="Ann")
-> Groups where there is users starting with the letters "Ann"
```
# Querying Many-to-many (M2M)
## Data
```python
class Movie()
    name = Charfield(...)

class Character()
    name = Charfield(...)
    movies = models.ManyToManyField(Movie)
```
# Example 1
```python
Character.objects.filter(movies__name="Civil War")
 - All characters in -Civil War-

Movie.objects.filter(character__name="Captain America")
 - All movies -Captain America- is in
```
# Example 2
```python
 Character.objects.get(name='Captain America').movies.all()
  - 

 Movie.objects.get(name="Avengers").character_set.all()
  - Django creates the 'character_set' automatically because the M2M is specified on
  - the Character class.

```